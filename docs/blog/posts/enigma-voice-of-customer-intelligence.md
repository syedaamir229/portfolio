---
date: 2025-09-15
authors:
  - syedaamir
categories:
  - AI & Automation
  - Data Science
description: "How a multilingual voice-of-customer platform combined ingestion, translation, Genie spaces, and retrieval workflows for self-serve insight access."
---

# Enigma: Building a Voice-of-Customer Intelligence Platform

Large volumes of social feedback are only useful if teams can query them quickly and consistently. This post walks through how we turned multilingual, unstructured audience commentary from four platforms into a practical decision-support system -- one where non-technical stakeholders could get answers in seconds instead of waiting days.

<!-- more -->

## The Problem: Arabic Social Feedback at Scale

At Shahid (MBC Group), audience feedback arrived constantly -- comments on social posts, replies to promotional tweets, reactions to short-form video content, YouTube commentary. The volume was not the issue. The issue was that none of it was practically usable.

The feedback was scattered across four platforms (Twitter, Facebook, YouTube, and the platform's own Shorts feature), each with its own schema, export format, and API behavior. The majority of comments were in Arabic, which limited direct analysis for parts of the leadership team. And there was no unified identifier linking a comment back to a specific title, season, or content entity across sources.

The result was a familiar pattern: analysts would manually pull exports, translate samples, tag sentiment by hand, and compile findings into slide decks. This worked when the content catalog was small. It did not scale. By the time a sentiment report was assembled, the content team had already moved on to the next release cycle.

The goal was not to build a research tool for data scientists. It was to give product managers, content leads, and executives a way to ask "what are audiences saying about this title?" and get a structured, cross-platform answer without filing a data request.

## Architecture: Bronze, Silver, Gold with NLP at Every Layer

The platform followed a three-layer ingestion architecture built on Databricks and Delta Lake, designed to move raw social data through progressive stages of cleaning, enrichment, and semantic modeling.

**Bronze (Raw Ingestion):** Each source -- Twitter posts and threaded replies, Facebook posts and comments via Funnel exports, YouTube video comments, and platform Shorts comments -- landed in its own raw Delta table with minimal schema enforcement. These tables served as the audit trail and reprocessing source. Daily ingestion jobs ran on a pre-10 AM SLA (Dubai time).

**Silver (Cleaning and Enrichment):** This is where the heavy lifting happened. Raw text went through a five-stage NLP enrichment pipeline:

1. **Arabic-to-English translation** -- making all content queryable regardless of source language
2. **Sentiment scoring** -- classifying each comment as positive, neutral, or negative
3. **Profanity detection** -- flagging inappropriate content for moderation workflows
4. **URL extraction and title mapping** -- parsing URLs from comment text and resolving them to internal content identifiers (mapping to a `dwh_parent_id` so every comment could be traced back to a specific show or season)
5. **Platform normalization** -- standardizing field names, timestamp formats, and source labels across all four platforms

Each source had its own staging table in the Silver layer, with enrichment applied consistently regardless of origin.

**Gold (Semantic Model):** The final layer consolidated everything into two core tables -- a post-level dimension table (`dim_post`) and a fully enriched comment-level fact table (`fact_comment`) -- plus an additional enriched table carrying extra metadata for LLM workflows. These tables powered everything downstream: Genie spaces, the FAISS vector index, the Supervisor Agent, and the application APIs.

The entire pipeline operated on daily scheduled jobs with clear SLAs: raw ingestion before 10 AM, enriched fact tables within one hour of ingestion, and the vector index rebuilt before 12 PM -- all timed so that insights were available before the content team's morning review cycle.

## Two Genie Spaces, Not One

An early and important architectural decision was to split query interfaces into two specialized Genie spaces rather than building a single general-purpose one.

**Comments Genie** handled quantitative insights derived from comment data: sentiment distribution across platforms, comment volume trends over time, profanity rates by content type, and title-level comment behavior for specific shows, seasons, or episodes. It was built on the enriched comment fact table and configured with curated sample prompts, field-level metadata, and explicit instructions to guide LLM-to-SQL generation.

**Engagement Genie** covered a different domain entirely: user engagement metrics like plays, playtime, first-watch behavior, and trends segmented by subscription type, geography, device, and demographics. It sat on top of a star schema with a fact table and multiple dimension tables (address, content, device, package, demographics).

The reason for the split was practical: LLM-to-SQL reliability degrades when a single Genie space covers divergent schemas and query intents. A question about "sentiment on Title X" and a question about "playtime trends for Title X" hit completely different tables with different join patterns. Keeping them separate reduced hallucination risk and improved query accuracy. Each space had its own curated instructions, sample queries, and allowed filters.

Both Genie spaces were consumed by the Supervisor Agent (described below) and were also available directly to analysts and leadership who wanted structured, SQL-backed answers without writing queries themselves.

## The Supervisor Agent and Routing Logic

On top of the Genie spaces and the FAISS vector index sat a LangGraph Supervisor Agent -- an orchestration layer that classified incoming queries and routed them to the right tool.

When a user asked a question, the agent determined whether the query was:

- **Quantitative** (e.g., "how many comments did Title X get last week?") -- routed to the appropriate Genie space for SQL execution
- **Qualitative** (e.g., "what are people saying about the ending of Season 2?") -- routed to the FAISS vector index for semantic retrieval over translated comment embeddings
- **Hybrid** -- requiring both a structured KPI answer and a narrative summary, in which case both tools were called and the results synthesized

This routing mattered because without it, users had to know which interface to use for which type of question. The Supervisor Agent removed that cognitive overhead. A content lead could ask a mixed question and get back both the numbers and the narrative, without understanding the underlying architecture.

The vector index itself used FAISS stored in Unity Catalog Volumes rather than a managed vector database. This was a deliberate cost-efficiency decision: the query volume did not justify managed vector search overhead, and a daily rebuild cycle met the SLA at significantly lower cost while Unity Catalog still provided lineage and access control.

## The Databricks App: Three Query Modes

The user-facing layer was a Databricks App built with React on the frontend and FastAPI on the backend, deployed as a single bundle through GitLab CI/CD.

The app exposed three interaction modes:

1. **Title Chat** -- a title-focused workspace where users selected a specific show or season from a dropdown, then saw sentiment distribution charts, comment clusters, and engagement KPIs at a glance. A chat panel let them ask follow-up questions about that title, with the Supervisor Agent automatically routing to SQL or vector search as needed.

2. **General Chat** -- a free-form conversational interface for open-ended exploration. Users could ask about cross-platform trends, compare audience behavior across titles, or surface complaint patterns without constraining the query to a single title.

3. **Custom Post Research** -- built in direct response to a recurring leadership request. A user could paste a Twitter URL or post ID, and the system would fetch all replies, run them through the Supervisor Agent and vector index, and return top themes, sentiment interpretation, and evidence-backed narrative. This turned what had been a multi-day manual analysis into an automated workflow.

The UI was intentionally minimal -- charts rendered via Plotly, responses formatted as structured Markdown, and session state preserved to avoid resets. The goal was to let stakeholders get to insight with as few interactions as possible.

## What Changed

Before this platform, audience insight workflows required analyst involvement at every step: pulling exports, translating, tagging, aggregating, and presenting. The typical turnaround for a sentiment report on a new title was measured in days.

After deployment:

- **Time to insight dropped from hours of manual work to seconds** via natural language query, with enriched data available daily before the content team's morning review
- **Cross-source analysis became possible for the first time** -- comment volume, sentiment, and engagement trends could be compared across Twitter, Facebook, YouTube, and Shorts in a single interface
- **SQL dependency dropped significantly** -- two Genie spaces provided self-service analytics for non-technical stakeholders, reducing ad hoc analyst query requests
- **Four social and platform sources were unified** into a single queryable model, eliminating the separate-export workflow that had been the default

The platform pattern -- multi-source ingestion, NLP enrichment at the Silver layer, a unified semantic model, and natural language query interfaces -- is directly transferable to any organization dealing with high-volume unstructured feedback: retail reviews, hospitality service feedback, financial services complaint intelligence, or healthcare patient sentiment. The specific tools (Databricks, FAISS, LangGraph) are implementation choices. The architecture holds regardless.

---

*For the full case study, see [Voice-of-Customer Intelligence Platform](../../projects/enigma.md).*

<div class="grid cards" style="margin-top: 3rem" markdown>

-   :material-chat-processing:{ .lg .middle } **Building something similar?**

    ---

    If your team is sitting on unstructured feedback data -- social comments, support tickets, reviews -- and needs a way to make it queryable without SQL, happy to share what worked here.

    [Let's Talk](https://mail.google.com/mail/?view=cm&fs=1&to=saamir259@gmail.com&su=Let%27s%20work%20together){ target=_blank rel=noopener .md-button .md-button--primary }
    [Full Case Study :material-arrow-right:](../../projects/enigma.md){ .md-button }

</div>
