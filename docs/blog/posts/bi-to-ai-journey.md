---
date: 2026-04-16
authors:
  - syedaamir
categories:
  - Career
description: "A decade of building data systems, from BI consulting foundations to production ML and AI automation. What changed, what stayed the same, and what I would tell my earlier self."
---

# From BI to AI: My 10-Year Journey in Data

I started my data career in 2016 building Tableau dashboards in a Dubai consulting firm. Ten years later, I'm designing AI automation systems and NLP platforms. This post is a reflection on how that progression happened, not as a planned career path, but as a series of problems that each demanded slightly different tools.

<!-- more -->

## The Consulting Years (2016-2022)

### Starting Point: Reports and Dashboards

My first projects were straightforward: take data from a database, build a dashboard, deliver it to a stakeholder. The tools were Tableau, later Power BI, sometimes Qlik. The work was satisfying because the feedback loop was tight. You could see a business user go from "I don't know" to "now I see it" in a single meeting.

What I learned in this phase was not about tools. It was about **translation**: converting business questions into data structures, and data structures into visual answers. This skill turned out to be foundational for everything that followed.

### Scaling Up: Multiple Clients, Multiple Industries

Working at consulting firms like Scan Technology, Al-Futtaim Technologies, and Beinex, I delivered BI across automotive (JLR), retail (Al-Futtaim), financial services (Adani), and enterprise IT. Each industry had different data shapes but similar patterns:

- Executives wanted KPIs they could trust
- Analysts wanted self-service access
- IT wanted governance and security
- Everyone wanted faster delivery

The repeatable lesson: **the data model matters more than the visualization**. A well-structured data layer makes every dashboard faster to build, easier to maintain, and more trustworthy. A poorly structured one makes every project a bespoke effort.

### The Turning Point: Enterprise Ownership

The biggest step in consulting was moving from project delivery to **enterprise ownership**, being the person responsible for a client's entire BI architecture, not just individual reports. At Beinex, I expanded a short-term consulting engagement into a multi-year delivery track. That shift from "build this report" to "own this capability" changed how I thought about data work.

## The Platform Years (2022-Present)

### Joining a Product Company

Moving from consulting to Shahid (MBC Group) was a different world. Instead of delivering to clients and moving on, I was building systems that I would have to live with. The data was bigger, the stakeholders were closer, and the feedback cycles were measured in days, not months.

### Phase 1: Data Foundations

The first year was pure data engineering. The platform had data, but it was scattered across disconnected systems with no shared model. I co-built an enterprise data model with fact and dimension tables in a layered Bronze/Silver/Gold architecture on Databricks and Delta Lake.

This was not glamorous work. It was table design, transformation logic, surrogate key management, and schema enforcement. But it was the foundation that made everything else possible.

### Phase 2: Analytics Maturity

With a trusted data layer in place, the next challenge was reporting governance. Teams were still building measures inside individual Power BI files, which meant the same KPI could have different definitions in different reports.

I built a semantic layer in SSAS Tabular, a governed model with DAX measures that standardized KPI definitions across the organization. Reports connected to this layer through live connections, so every dashboard consumed the same metric logic.

Alongside this, I built the revenue operations pipeline, an end-to-end system connecting Google Ad Manager data through ingestion, processing, and alerting to Power BI dashboards and Slack notifications.

### Phase 3: Data Science

The data model and semantic layer created something unexpected: a clean feature foundation for machine learning. The same subscriber features that powered BI dashboards could feed clustering models, inference workflows, and propensity scoring.

I built viewing behavior clustering, behavior-based attribute inference, and a profile-level feature store, all consuming the same Gold-layer tables. The key insight: **ML features and BI metrics should share the same data layer**. When they diverge, you get models that disagree with dashboards.

### Phase 4: AI Automation

The most recent work has been in AI automation, systems that do not just analyze data but act on it. Two projects defined this phase:

1. **CRM Campaign Automation**: A scenario engine that encodes targeting rules and pushes audiences to CleverTap for scheduled campaign activation. This replaced manual analyst handoffs with structured, repeatable workflows.

2. **Voice-of-Customer Intelligence**: A multilingual NLP platform that ingests social media comments, translates Arabic text, and makes the data queryable through Genie spaces and a retrieval agent. Business users can ask questions in natural language instead of requesting SQL queries.

Both projects share a pattern: **structured AI that augments human workflows rather than replacing them**. The CRM team still designs campaigns, they just do not need an analyst to build every audience. The content team still interprets customer feedback, they just do not need SQL to access it.

## What Changed, What Stayed

### What Changed

- **Scale**: From thousands of rows to billions
- **Tools**: From Tableau to Databricks, from SSIS to PySpark, from scheduled reports to real-time agents
- **Scope**: From dashboard delivery to full-stack data systems
- **Impact model**: From "here is your report" to "here is a system that runs itself"

### What Stayed the Same

- **Translation**: Converting business problems into data structures is still the core skill
- **Data modeling**: Getting the grain right, designing clean fact/dimension relationships, tracking entity lifecycles. This matters just as much with ML as with BI
- **Stakeholder trust**: The fanciest system is worthless if people do not trust the numbers
- **Incremental delivery**: Every successful project I have been part of was delivered in phases, not as a big bang

## What I Would Tell My Earlier Self

**Don't skip the foundations.** The consulting years felt like "just BI" at the time, but the modeling skills, stakeholder communication patterns, and delivery discipline from that phase are what make the AI work effective now.

**The stack does not matter as much as you think.** I have worked with Tableau, Power BI, Qlik, Alteryx, SSIS, Databricks, PySpark, scikit-learn, and various LLM frameworks. The tool changes; the thinking does not. Learn to model data well, and you can pick up any tool.

**Own the problem, not the ticket.** The biggest career jumps came from taking ownership of a capability, not from completing individual tasks. Building a report is a task. Owning a client's BI architecture is a career move. Building a feature table is a task. Designing a data foundation that serves BI, ML, and AI is a career move.

**AI is not a separate career.** The progression from BI to data science to AI automation felt natural because each phase built on the previous one. The data model enabled the semantic layer. The semantic layer enabled the feature store. The feature store enabled the automation platform. It is one continuous thread.

---

*I'm currently based in Dubai, UAE, working as a Data & AI Solutions Engineer. If you're navigating a similar transition or building data systems in the MENA region, feel free to [connect on LinkedIn](https://www.linkedin.com/in/syedaamiruddin/).*

<div class="grid cards" style="margin-top: 3rem" markdown>

-   :material-account-arrow-right:{ .lg .middle } **Navigating a similar transition?**

    ---

    Happy to share what worked and what didn't. Connect on LinkedIn or reach out by email if you want to talk through your setup.

    [Let's Talk](https://mail.google.com/mail/?view=cm&fs=1&to=saamir259@gmail.com&su=Let%27s%20work%20together){ target=_blank rel=noopener .md-button .md-button--primary }
    [LinkedIn :material-arrow-top-right:](https://www.linkedin.com/in/syedaamiruddin/){ .md-button target=_blank rel=noopener }

</div>
