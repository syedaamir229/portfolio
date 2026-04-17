---
date: 2023-07-10
slug: semantic-layer-architecture-data-flow
authors:
  - syedaamir
categories:
  - BI & Analytics
  - Data Engineering
description: "A practical architecture blueprint for semantic layers: source model boundaries, tabular build flow, deployment artifacts, and consumer patterns."
---

# Semantic Layer Series Part 2 of 6: Architecture and Data Flow Blueprint

A semantic layer only works if architecture boundaries are clear. Many implementations fail because they mix data engineering responsibilities, metric engineering responsibilities, and report development responsibilities in one place.

<!-- more -->

This implementation used a clean separation: curated tables feed the semantic model, the model owns KPI logic, and reports consume measures through live connections.

<figure markdown>
  ![Semantic layer architecture and data flow](../../assets/diagrams/semantic-series-02-architecture-flow.svg){ .diagram-embed }
  <figcaption>Clear boundaries between data preparation, semantic model build, and report consumption keep ownership and quality controls simple.</figcaption>
</figure>

## Layer Boundaries

### 1. Curated table layer

This layer contains conformed fact and dimension tables, plus selected staging helper tables where needed for model efficiency.

Common table families:

- lifecycle facts (`fact_subscriptions`, movement tables)
- engagement facts (`fact_engagement`)
- ad facts (`fact_ad_events`)
- conformed dimensions (`dim_subscriber`, `dim_content`, `dim_device`)

The semantic layer should not correct broken upstream pipelines. It should consume governed inputs.

### 2. Semantic model build layer

This is where metrics become reusable products:

- tabular project definition
- relationships and hierarchies
- base and business measures
- role-based access
- partition strategy

Build outputs are packaged as deployment artifacts to keep releases controlled and reproducible.

### 3. Consumption layer

Report developers connect live to the model and focus on layout, storytelling, and decision support. KPI logic remains in the semantic model.

## End-to-End Data Flow

The flow used in this project:

1. Upstream jobs produce refreshed curated tables.
2. Semantic model build process updates structures and measures in development.
3. Validation checks pass before deployment.
4. Deployment artifact promotes changes while preserving critical objects.
5. Refresh process updates partitions and processes the model.
6. Reports consume updated measures without local reimplementation.

This sounds straightforward, but only works reliably when change control and refresh control are treated as first-class engineering concerns.

## Design Decisions That Reduced Failure Modes

### Keep helper transformations outside report files

Certain mapping and compression logic belongs in data preparation or model tables, not in individual reports.

### Preserve partition and role continuity during deployment

Replacing a model without preserving partition and role state creates avoidable operational risk.

### Separate model engineering from report engineering

Model engineering focuses on correctness, reuse, and performance. Report engineering focuses on usability and narrative. Blending both in each report slows everything.

## Minimal Architecture Checklist

Before scaling a semantic layer, confirm these controls are in place:

- curated table inputs have stable grain and keys
- measure ownership and review path are documented
- deployment process preserves partitions and roles
- refresh dependency order is explicit
- report teams consume shared measures by default

## What This Enables

Once architecture is stable, adding metrics becomes safer and faster because:

- measure logic is reusable
- dashboard delivery time drops
- KPI changes are governed and auditable
- quality checks become repeatable

This is the point where semantic layers stop being a BI improvement and become an analytics operating model.

## Build Walkthrough: From Curated Tables to Live Model

### Prerequisites

- curated fact and dimension tables with stable keys
- semantic model project (SSDT or equivalent tabular project)
- DAX authoring and model validation tooling
- deployment process that preserves roles and partitions

### Step 1: Validate source-table contracts

Before modeling, validate grain and key health with SQL baselines.

```sql
-- Example: verify one row per subscriber per day in movement fact
SELECT
  event_date,
  COUNT(*) AS rows_in_day,
  COUNT(DISTINCT subscriber_id) AS subscribers_in_day
FROM fact_subscriptions
GROUP BY event_date
ORDER BY event_date DESC;
```

If grain is unstable, stop and fix upstream. Do not encode data corrections inside report logic.

### Step 2: Build model objects in this order

1. add dimensions (`dim_subscriber`, `dim_content`, `dim_device`, `dim_date`)
2. add facts (`fact_subscriptions`, `fact_engagement`, `fact_ad_events`)
3. define relationships and cardinality explicitly
4. mark date table and time hierarchy
5. add base measures only

### Step 3: Add governance objects

1. create role definitions for access scope
2. group measures by domain folders
3. add descriptions for business-critical fields and measures
4. run model best-practice checks before deployment

### Step 4: Configure refresh and partitions

1. define partitioning by date for large fact tables
2. set processing order: dimensions first, then facts
3. configure incremental ranges per fact family
4. validate partition counts after processing

### Step 5: Publish to report consumers

1. deploy model to target semantic server
2. publish BI dataset with live connection
3. migrate pilot reports to semantic measures only
4. validate report outputs against baseline checks

### Done Criteria

- all pilot dashboards run on semantic measures only
- KPI deltas are explained and documented
- no local KPI formulas remain in pilot reports
- refresh pipeline completes within agreed SLA

## Key Takeaway

Architecture quality is what makes semantic layers sustainable. If boundaries are clear and deployment controls are disciplined, teams can scale metric coverage without scaling confusion.

---

*For the full case study, see [Enterprise Semantic Layer & KPI Framework](../../projects/semantic-layer.md).* 

<div class="grid cards" style="margin-top: 3rem" markdown>

-   :material-chart-line-variant:{ .lg .middle } **Continue the series**

    ---

    Next: how KPI logic is engineered in DAX, including measure layering and time-intelligence design.

    [Read Part 3 :material-arrow-right:](./semantic-layer-03-kpi-engineering-with-dax.md){ .md-button .md-button--primary }
    [View Case Study :material-briefcase-outline:](../../projects/semantic-layer.md){ .md-button }

</div>
