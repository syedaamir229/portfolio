---
date: 2022-10-15
authors:
  - syedaamir
categories:
  - Data Engineering
description: "Practical lessons from building an enterprise data model at Shahid (MBC Group), from disconnected sources to a unified Silver and Gold architecture."
---

# Building a Scalable Data Model: Lessons from a Streaming Platform

Every data team eventually hits the same wall: different teams, different queries, different numbers. This post covers the design of a layered data model that unified reporting at Shahid (MBC Group) and became the foundation for everything that came after, including semantic layers, ML features, and AI automation.

<!-- more -->

## Why the Model Mattered

At Shahid (MBC Group), data lived in separate systems. Subscriptions came from one platform. Viewing data from another. Content metadata from a third. Ad delivery data from yet another.

Each team wrote their own SQL to answer their own questions. The result was predictable: the same metric, calculated three different ways, producing three different numbers. Leadership meetings turned into reconciliation exercises instead of decision-making sessions.

The fix was not a dashboard or a new tool. It was a shared, governed data layer that every downstream consumer could trust.

## The Architecture

### Bronze Layer: Raw Ingestion

Source systems land in Delta Lake tables on S3 with minimal transformation. The goal is capture, not cleanup:

- `raw_views`: play events from the video analytics platform
- `raw_subscriptions`: subscription events from the billing system
- `raw_content`: content metadata from the catalog system
- `raw_ad_events`: ad delivery data from Google Ad Manager

Schema enforcement happens at ingestion. Data quality checks run on arrival. But the Bronze layer preserves the raw structure with no business logic applied yet.

### Silver Layer: Standardized Model

This is where the real modeling happens. The Silver layer implements a dimensional model with clear fact and dimension relationships.

**Fact tables:**

- `fact_view`: one row per play event, each with a unique view ID linked to subscriber, content, device, and address dimensions
- `fact_subscription`: one row per subscription record, with lifecycle tracking (new, renewed, cancelled, reactivated)
- `fact_ad_events`: ad inventory, impressions, errors, and revenue

**Dimension tables:**

- `dim_user`: subscriber profiles with status tracking (registered, subscribed, anonymous, inactive)
- `dim_content`: content hierarchy (series, season, episode, movie) with parent-child relationships linking episodes to seasons to shows
- `dim_package`: subscription package details, tiers, and a package hierarchy index to support upgrade and downgrade calculations
- `dim_device`: device metadata (type, model, vendor)

A key design decision was **subscriber movement tracking**. Instead of storing only the current state, the model tracks transitions: who upgraded, who churned, who came back. The `daily_movement` table captures these events (new, churned, reactivated, upgraded, downgraded) at a daily grain, making lifecycle analytics possible without complex window functions at query time.

The content hierarchy also required careful design. Content metadata came from multiple catalog systems, and the model needed to represent the relationship between episodes, seasons, and shows consistently. Getting this hierarchy right was critical because downstream consumers needed to aggregate viewing data at different levels: per-episode for engagement analysis, per-season for completion tracking, and per-show for content investment decisions.

### Gold Layer: Aggregates and Features

The Gold layer serves two audiences:

1. **BI teams** get pre-aggregated metrics: daily movements, content performance summaries, regional breakdowns
2. **Data science** gets feature tables: user-level features (tenure, engagement cohort, churn risk score, content preferences, hours consumed by genre) and user-content features (binge indicators, content viewing cohorts, completion rates)

This dual-purpose design meant the same governed data fed both reporting and ML, with no divergence and no separate pipelines. A subscriber's churn risk cohort in a Power BI dashboard was the same value that fed the clustering model, because both consumed the same Gold-layer table.

## Five Lessons Learned

### 1. Start with the Grain, Not the Dashboard

It is tempting to start with "what reports do we need?" and work backward. Starting with the grain of the fact tables is far more effective. What is the lowest-level event that needs to be captured? Everything else builds up from there.

For viewing data, the grain was a single play event (a unique view). For subscriptions, it was a single subscription record. Getting this right early saved months of rework later.

### 2. Movement Tracking Is Worth the Complexity

Tracking subscriber transitions (new, churned, reactivated, upgraded, downgraded) at the model level was one of the highest-value decisions. Before this, calculating churn required complex queries that different teams implemented differently. After, it was a column. The subscription fact table carried flags like `is_acquisition`, `is_reconnect`, and `is_winback` directly on the record, which meant movement analysis that previously took hours of SQL work became a simple filter.

### 3. Surrogate Keys Everywhere

Every entity gets a generated surrogate key. Source system IDs are preserved but never used as join keys. This insulates the model from source system changes. When the billing platform migrated IDs, the model absorbed it without breaking downstream queries.

### 4. Feature Tables Belong in the Model

Feature engineering often lives in notebooks, disconnected from the data model. By making feature tables first-class Gold-layer objects, the team ensured that ML features stayed consistent with BI metrics. A subscriber's "engagement cohort" meant the same thing in a Power BI dashboard and a clustering model.

### 5. Build for the Next Consumer

The model was designed for reporting, but it turned out to be the foundation for the semantic layer, the ML feature store, and the CRM automation platform. This happened not because the next use cases were predicted, but because the model was designed around business entities rather than report requirements.

## The Pattern

This architecture pattern works for any organization with multiple source systems and multiple consumers:

| Component | Streaming | Retail | SaaS | Fintech |
|-----------|-----------|--------|------|---------|
| Core fact | Views, Subscriptions | Orders, Cart Events | Usage Events, Subscriptions | Transactions, Applications |
| Key dimension | Content, Subscribers | Products, Customers | Features, Accounts | Products, Customers |
| Movement tracking | Subscriber lifecycle | Customer lifecycle | Account lifecycle | Account lifecycle |
| Feature layer | Viewing behavior | Purchase behavior | Product usage | Risk signals |

The specifics change, but the architecture stays the same: capture raw, model in Silver, aggregate and featurize in Gold.

## Key Takeaway

A data model is not a one-time project. It is infrastructure. The initial build took months, but the payoff compounded over years. Every subsequent project, from semantic layers to ML features to AI automation, built on this foundation rather than starting from scratch.

If a team is still reconciling numbers in meetings, the fix is not a better dashboard. It is a shared data model that everyone trusts.

---

*For the full case study, see [Enterprise Data Model](../../projects/data-model.md).*

<div class="grid cards" style="margin-top: 3rem" markdown>

-   :material-database:{ .lg .middle } **Building a data model for your team?**

    ---

    Read the full case study for the architecture details, or get in touch if you want to talk through your specific setup.

    [Let's Talk](https://mail.google.com/mail/?view=cm&fs=1&to=saamir259@gmail.com&su=Let%27s%20work%20together){ target=_blank rel=noopener .md-button .md-button--primary }
    [Full Case Study :material-arrow-right:](../../projects/data-model.md){ .md-button }

</div>
