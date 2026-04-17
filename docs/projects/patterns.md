---
title: Delivery Patterns & Playbooks
description: Reusable data and AI delivery patterns extracted from real projects — each with context on when to apply it, what decisions it involves, and when it is NOT appropriate.
hide:
  - navigation
---

# Delivery Patterns & Playbooks

<p class="hero-credential">6 patterns &nbsp;·&nbsp; Architectural &nbsp;·&nbsp; Operational</p>

These are the reusable delivery patterns that appear across my projects. Each one is a real architectural or operational decision that solved a specific class of problem — not a theoretical framework.

Each pattern links to the originating case study where you can see it applied in full context.

---

## Patterns

<div class="grid cards" markdown>

-   [Layered Data Architecture (Silver + Gold)](data-model.md)

    ---

    **What it solves:** Siloed source systems producing conflicting team-level query logic; BI and ML workloads requiring different table structures from the same data.

    **How it works:** Source systems feed into a Silver layer (normalized fact and dimension tables for BI) and a Gold layer (pre-aggregated feature store tables for ML/AI). Each downstream consumer gets its natural grain without forcing trade-offs on the shared model.

    **Key decision:** Keep Silver and Gold separate rather than a single unified layer — this allows BI dashboards, semantic models, and ML feature pipelines to consume from appropriate layers independently.

    **When NOT appropriate:** If your analytics team is small (1–3 people doing exploratory work) and you have a manageable number of sources, a simpler single-layer approach is sufficient. The layered architecture pays off when you have multiple downstream consumers and growing data volumes.

    <span class="project-tag">Applied in: Enterprise Data Model</span>

-   [Governed Semantic Layer](semantic-layer.md)

    ---

    **What it solves:** The same KPI calculated differently across departments; analysts spending time reconciling numbers rather than analyzing them.

    **How it works:** Business logic is defined once as governed DAX measures in a semantic model (SSAS Tabular or equivalent). All reports connect via live connection — updates to a measure propagate everywhere automatically. A Measures Glossary documents every definition.

    **Key decision:** Build the semantic layer on top of Gold tables (not directly on source systems) — this keeps the semantic model focused on business definitions, not data transformation.

    **When NOT appropriate:** For organizations with fewer than 3–5 active reporting teams or modest data volumes, a simpler shared Power BI dataset with certified measures covers the governance need without SSAS infrastructure overhead.

    <span class="project-tag">Applied in: Semantic Layer & KPI Framework</span>

-   [Detect-Alert-Act Pipeline](ad-pipeline.md)

    ---

    **What it solves:** Operational issues (pacing shortfalls, error spikes, delivery anomalies) discovered late — after the window to intervene has passed.

    **How it works:** Daily ingestion pipeline → business-logic derivation at staging → threshold-based alerting (Slack or equivalent) → reporting layer for investigation. Historical refresh on a separate cadence (e.g., weekly) captures late-arriving data without rebuilding the daily pipeline.

    **Key decision:** Centralize derived measures at the staging layer rather than building per-team pipelines — this ensures all teams calculate consistent totals from a shared source, preventing revenue discrepancies.

    **When NOT appropriate:** If your operational data is already available in a platform with native alerting (e.g., Looker Studio + GAM), a custom pipeline adds overhead rather than value. The pattern becomes necessary when you need cross-source attribution or derived business logic not available in native reporting.

    <span class="project-tag">Applied in: Revenue Ops Pipeline & Alerting</span>

-   [Phased BI Migration](bi-migration.md)

    ---

    **What it solves:** Big-bang BI migrations that fail because tool changes, data layer changes, and metric governance changes happen simultaneously.

    **How it works:** Three sequential phases with independent validation gates: (1) tool transition with equivalence verification, (2) data layer alignment to new architecture, (3) metric governance consolidation into shared layer. Each phase is independently testable and reversible.

    **Key decision:** Sequence the phases rather than running them in parallel — this contains the blast radius of any single phase failure and allows reporting teams to adapt incrementally.

    **When NOT appropriate:** For small BI estates (fewer than 10–15 active reports) or early-stage organizations, a direct migration is faster and the phased overhead is not justified.

    <span class="project-tag">Applied in: BI Modernization Roadmap</span>

-   [Profile-Level Recommendation with Account-Level Delivery](jarvis.md)

    ---

    **What it solves:** Personalization systems that lose accuracy in multi-profile households because they process at the account (billing) level rather than the individual viewer level.

    **How it works:** Recommendations are generated at the profile level (individual viewer behavior). An account rollup step selects the best profile per account using a configurable method (primary profile = highest watch hours, dominant profile = >60% household watchtime, last-active). A scenario selector applies behavioral prioritization before delivery.

    **Key decision:** Process at profile granularity, roll up to account for delivery — rather than processing at account level (simple but imprecise) or attempting to deliver per-profile (technically infeasible in most CRM platforms).

    **When NOT appropriate:** If your user base is single-profile per account, or if your CRM platform natively handles profile-level targeting, this pattern adds complexity without benefit. Also not appropriate if personalization accuracy at the profile level is not measurably different from account-level recommendations for your use case.

    <span class="project-tag">Applied in: CRM Campaign Automation Platform</span>

-   [Multi-Source NLP Pipeline with Tiered Query Interfaces](enigma.md)

    ---

    **What it solves:** High-volume unstructured feedback (comments, reviews, social posts) that is expensive to analyze manually, especially across multiple languages and platforms.

    **How it works:** Bronze → Silver NLP enrichment (translation, sentiment, profanity, entity tagging, platform normalization) → Gold semantic layer → tiered query interfaces: SQL-based Genie spaces for quantitative KPIs, FAISS vector index for semantic retrieval, LangGraph Supervisor Agent for mixed-intent queries. A production app (React + FastAPI) surfaces all three query modes in one interface.

    **Key decision:** Two specialized Genie spaces (by domain) rather than one general space — because LLM-to-SQL reliability degrades when a single space covers divergent schemas and query intents.

    **When NOT appropriate:** If feedback volume is low (<a few thousand records/day) or your team already has a structured NLP pipeline, a simpler approach (direct Genie space over existing tables, or a single RAG endpoint) is sufficient. The full Bronze→Gold stack with tiered query interfaces is justified only when source schemas diverge significantly and daily freshness at scale is required.

    <span class="project-tag">Applied in: Voice-of-Customer Intelligence Platform</span>

</div>

---

<div class="cta-card" markdown>

### Have a problem that matches one of these patterns?

[Let's talk about a project](https://mail.google.com/mail/?view=cm&fs=1&to=saamir259@gmail.com&su=Pattern%20inquiry&body=Hi%20Syed%2C%0A%0AI%20saw%20your%20delivery%20patterns%20page.%20I%27m%20dealing%20with%20%5Bproblem%5D%20and%20think%20pattern%20%5BX%5D%20might%20apply.%20I%27d%20like%20to%20discuss.){ target=_blank rel=noopener .md-button .md-button--primary }

</div>
