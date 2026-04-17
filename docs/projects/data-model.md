---
title: Enterprise Data Model for Streaming Analytics
description: How I co-built a unified Silver and Gold data model integrating 5 source systems — becoming the shared foundation for BI, ML, and AI across a MENA OTT platform.
tags:
  - Data Engineering
  - Data Architecture
  - Databricks
  - Delta Lake
hide:
  - navigation
---

# Enterprise Data Model for Streaming Analytics

!!! success "Outcome"
    30% faster data retrieval, 99% data accuracy, and a shared foundation that enabled every subsequent analytics, ML, and AI project on the platform.

*From siloed source systems with conflicting team-level query logic → one shared Silver/Gold model that became the foundation for BI, ML, and AI.*

!!! abstract "Case Study Summary"
    **Organization**: Shahid (MBC Group)
    **Role**: BI & Analytics
    **Timeline**: May 2022 – December 2022 (8 months)
    **Industry**: Media & Entertainment — Streaming Analytics
    **Ownership**: Key contributor to architecture design and implementation within the data engineering team; focused on fact/dimension table modeling and feature store structure

    **Constraints**: Source system schemas varied significantly across vendors (viewing from Youbora, subscriptions from Evergent, content metadata from Mediagenix/GA, ads from GAM); data volumes were growing rapidly and ad hoc query patterns were already causing instability; model had to serve both BI and data science use cases without creating separate ETL workstreams.

    **Impact Metrics**:

    - **5 source systems integrated** into a unified model: Youbora (viewing analytics), Evergent (subscription management), Mediagenix/Google Analytics (content metadata), Google Ad Manager (ad data), AWS S3 (raw storage layer)
    - **~15 tables** delivered: fact tables (viewing events, subscriptions), dimension tables (user, content, package, device, partner, address), and feature store tables (user-level and user-content behavioral features)
    - **2-layer architecture** (Silver: normalized fact/dim tables; Gold: derived feature store) — allowing BI workloads and ML models to consume from appropriate layers without coupling
    - Metric disputes across teams **eliminated** for core KPIs (subscriber counts, engagement metrics, churn/movement) — previously different teams used different SQL logic and produced conflicting numbers
    - Model became the **single shared foundation** for all downstream work: Semantic Layer, Revenue Ops Pipeline, Jarvis CRM Automation, and Enigma AI Platform — none of which could exist without it
    - **30% faster data retrieval** through standardized data structures, optimized table relationships, and indexing strategies
    - **99% data accuracy** for cross-functional teams accessing curated data, improving decision-making efficiency
    - Scaled to handle a **5x increase in data volume** with zero downtime during high-traffic periods

    *Verification: Model adoption tracked by downstream project dependencies; metric consistency validated through cross-team reporting alignment.*

A unified enterprise data model was required to stop conflicting numbers across teams. Source systems were disconnected, and each team used separate SQL logic to calculate similar metrics — creating reporting friction and analysis delays.

## Challenge

- **Disconnected source systems**: Subscription, viewing, content, and ad data lived in separate vendor platforms with incompatible schemas
- **Conflicting reporting logic**: Each team wrote their own queries against raw tables, producing inconsistent KPIs for the same business question
- **Slow cross-team analysis**: Any cross-domain analysis (e.g., content performance by subscription type) required manual join work before results could be trusted
- **Growth pressure**: Rising data volumes were making ad hoc queries against raw source tables increasingly unstable and slow

## Approach

**Key decision made along the way:**

> **Decision — Separate Silver (fact/dim) and Gold (feature store) layers rather than a single flat model**
> *Problem*: BI workloads need normalized, join-ready tables for dashboards; ML/data science workloads need pre-aggregated, feature-engineered tables with behavioral signals.
> *Options*: Single unified layer (simpler but mixes grain); Silver + Gold separation (more structure but serves both consumers cleanly).
> *Chosen*: Two-layer architecture: Silver for normalized fact and dimension tables, Gold for derived feature store.
> *Why*: A single layer forces trade-offs — either BI queries are slower (if the layer is feature-store-shaped) or ML features have to be computed at query time (if the layer is BI-shaped). Separating the layers allows each consumer to operate at its natural grain. This proved essential as downstream workloads expanded: Jarvis and Enigma consume Gold features; the semantic layer and dashboards consume Silver tables.

- Designed layered architecture with clear Silver and Gold responsibilities and table ownership standards
- Built fact tables for core business events: viewing sessions (`fact_view`, `fact_view_features`) and subscriptions (`fact_subscription`, `fact_subscription_features`)
- Built dimension tables for all core entities: user, content (episode/season/show hierarchy), package, device, partner, address, promotion
- Implemented `daily_movement` table for subscriber lifecycle tracking: new, churned, upgraded, downgraded, reactivated events — enabling cohort and churn analysis
- Built user-level and user-content feature store tables (`user_features`, `user_content_features`) as the Gold layer for ML and personalization consumers
- Aligned model requirements with analytics and data science teams before build — ensuring downstream use cases remained consistent and no rework was required

## Architecture Overview

<figure markdown>
  ![Enterprise Data Model: 5 source systems feed into S3 raw storage, then Silver layer (fact and dimension tables), then Gold layer (feature store tables), consumed by BI reporting, semantic layer, ML models, and AI platforms](../assets/diagrams/data-model.svg){ .diagram-embed }
  <figcaption>Source systems (Youbora, Evergent, GAM, Mediagenix) flow through S3 raw storage into Silver fact/dimension tables and Gold feature store tables, consumed by downstream BI, ML, and AI workloads</figcaption>
</figure>

## Results & Impact

- **What changed in operations**: Teams stopped maintaining parallel query logic — one shared model replaced the fragmented per-team SQL workbooks that had been the source of metric disputes
- **What changed in decisions**: Cross-domain analysis (content performance by subscription type, ad revenue by viewer segment) became reproducible and fast — instead of requiring custom joins and reconciliation each time
- **Foundation leverage**: Every subsequent project built on this model — the Semantic Layer, Revenue Ops Pipeline, Jarvis CRM automation, and Enigma AI platform all depend on it directly. The ROI of the data model compounds with each downstream use case.
- **Scalability**: Delta Lake on S3 with ACID transactions enabled reliable incremental processing at growing data volumes — replacing the ad hoc query instability that had emerged before the model

## Tech Stack

- **Platform**: Databricks (PySpark + Spark SQL)
- **Storage**: AWS S3, Delta Lake (ACID transactions)
- **Sources**: Youbora, Evergent, Google Analytics/Mediagenix, Google Ad Manager
- **Architecture**: Bronze (raw) → Silver (fact/dim) → Gold (feature store)

## Reusable Pattern

This Silver/Gold layered architecture pattern — normalized fact/dim tables for BI, derived feature store for ML/AI — applies to any organization with siloed source systems and growing analytics/ML ambitions:

- **Retail and e-commerce**: Customer, order, product, and campaign data unified for both BI reporting and recommendation model features
- **Fintech**: Customer, transaction, and risk data structured for both compliance reporting and fraud detection models
- **Telecom**: Subscriber, usage, package, and billing data consolidated into one layer shared by both BI dashboards and churn prediction models
- **Healthcare**: Patient, provider, and operational data integrated once and consumed by both reporting and clinical ML use cases

**When this pattern is NOT appropriate**: If your data fits in a single database and your analytics team is small (1–3 people running exploratory work), the infrastructure overhead of a layered Databricks model is not justified. The layered architecture pays off when you have multiple downstream consumers (BI + ML + AI), when source system schemas diverge significantly, or when data volumes make ad hoc queries against raw tables unreliable.

---

## Related Projects

[Semantic Layer & KPI Framework](semantic-layer.md) · [Revenue Ops Pipeline & Alerting](ad-pipeline.md) · [CRM Campaign Automation](jarvis.md) · [Voice-of-Customer Intelligence](enigma.md)

---

<div class="grid cards" style="margin-top: 3rem" markdown>

-   :material-database:{ .lg .middle } **Solving the same problem in your organisation?**

    ---

    If your teams are working from separate query logic, producing conflicting numbers, or if cross-domain analysis requires manual reconciliation — a shared data model with clear layer responsibilities is the fix. Happy to talk through your source systems and what a practical architecture looks like.

    [Let's talk about a project](https://mail.google.com/mail/?view=cm&fs=1&to=saamir259@gmail.com&su=Project%20inquiry%3A%20Data%20model%20%2F%20data%20architecture&body=Hi%20Syed%2C%0A%0AI%20saw%20your%20Data%20Model%20case%20study.%20We%27re%20dealing%20with%20%5Bproblem%5D%20and%20I%27d%20like%20to%20discuss%20%5Bapproach%5D.%0A%0ATimeline%3A%20%5Bx%5D){ target=_blank rel=noopener .md-button .md-button--primary }

</div>
