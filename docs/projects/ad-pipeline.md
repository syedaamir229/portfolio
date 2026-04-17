---
title: Revenue Operations Data Pipeline & Alerting Platform
description: How I replaced spreadsheet-based ad revenue tracking with a 4-pipeline production system — with Slack pacing alerts and a 14-day historical refresh strategy for a MENA OTT platform.
tags:
  - Data Engineering
  - Revenue Operations
  - Alerting
  - Databricks
  - GAM API
hide:
  - navigation
---

# Revenue Operations Data Pipeline & Alerting Platform

!!! success "Outcome"
    25% faster data processing, same-day revenue pacing alerts, and a unified data layer replacing manual spreadsheet tracking across 3 teams.

*From spreadsheet-based revenue tracking with no alerts → 4-pipeline production system with same-day pacing visibility.*

!!! abstract "Case Study Summary"
    **Organization**: Shahid (MBC Group)
    **Role**: BI & Analytics
    **Timeline**: November 2023 – December 2024 (14 months)
    **Industry**: Media & Entertainment — Revenue Operations / Ad Tech
    **Ownership**: Primary owner of pipeline design, implementation, alerting logic, and reporting

    **Constraints**: GAM API rate limits required careful ingestion scheduling; ad impression data settles over 14 days due to late-arriving attribution, requiring a weekly historical refresh on top of daily runs; multiple teams (Ad Ops, Growth, Data) needed different views of the same underlying data without separate pipelines.

    **Impact Metrics**:

    - **4 production pipelines** built and operationalized: Inventory, Impressions, VAST Errors, Delivery Pacing — replacing ad hoc spreadsheet workflows for all four areas
    - **3 teams** now share a single data layer (Ad Ops, Growth, Data & Numbers) — previously each team tracked revenue in separate exports with no cross-team consistency
    - **9 raw + 10 derived measures** standardized across instream and display formats (SVOD and AVOD), including inventory, impressions, revenue, drop-off, and error rates
    - Pacing and VAST error issues now surfaced **same-day via Slack alerts** — previously discovered during post-campaign review or weekly reporting cycles
    - **Daily pipeline runs at 7AM** + 14-day historical refresh every Sunday to capture late-arriving GAM attribution data
    - Content-level and country-level revenue slicing enabled for the first time
    - **25% reduction in data processing times** through streamlined ETL for ad data ingestion

    *Verification: Pipeline run history tracked in Databricks Jobs; alert delivery confirmed via Slack channel audit; data adoption measured by BI report query volume.*

Revenue tracking was fragmented before this build. Inventory, impressions, pacing, and error data were tracked in separate spreadsheets with no automated pipeline, no cross-source analysis, and no proactive alerting.

## Challenge

- **No production pipeline**: Critical revenue operations data was entirely manual — high effort, error-prone, and not scalable
- **Limited observability**: Requests, impressions, bookings, and errors were not connected end-to-end in a single view
- **Late issue detection**: Pacing shortfalls and VAST error spikes were discovered in post-campaign reviews rather than in time to act
- **Low analytical flexibility**: Country, region, format, and content-level slicing required custom query work each time

## Approach

**Key decision made along the way:**

> **Decision — Unified staging layer with derived measures rather than per-team pipelines**
> *Problem*: Three teams needed the same underlying GAM data but with different slicing and business logic applied.
> *Options*: Build separate pipelines per team; build a shared raw + staging layer with derived fields.
> *Chosen*: Shared raw → staging architecture with derived measures (ad format, ad type, VOD model, device group, content ID via regex) applied once at the staging layer.
> *Why*: GAM API rate limits make it wasteful to pull the same data multiple times. Centralizing derivation logic ensures all teams calculate the same totals — especially important for revenue figures where discrepancies between teams were a previous source of friction.

- Built GAM API ingestion → S3 raw storage → Databricks staging pipeline with schema normalization
- Implemented 4 parallel pipelines: Inventory (sell-able ad slots), Impressions (delivered ads + revenue), VAST Errors (error categorization by type), Delivery Pacing (actual vs. booked delivery rate)
- Applied business derivation logic at staging: Ad Format, Ad Type (programmatic/direct), VOD Model (SVOD/AVOD), Device Category Group, Content Media ID via regex
- Configured daily run at 7AM + 14-day Sunday historical refresh to capture late-arriving impression and revenue attribution
- Added Slack alerting for pacing shortfalls and VAST error spikes — triggered from pipeline outputs with configurable thresholds
- Delivered Power BI dashboards connected to staging layer for multi-dimensional slicing (country, region, format, content)

## Architecture Overview

<figure markdown>
  ![Revenue Operations Pipeline: GAM API feeds into 4 processing pipelines (Inventory, Impressions, VAST Errors, Delivery Pacing) via S3 raw storage and Databricks staging, with outputs to Power BI dashboards and Slack pacing alerts](../assets/diagrams/ad-pipeline.svg){ .diagram-embed }
  <figcaption>Google Ad Manager API feeds into four processing pipelines (Inventory, Impressions, VAST Errors, Delivery Pacing) via S3 raw storage and Databricks staging, with outputs to Power BI dashboards and Slack alerts</figcaption>
</figure>

## Results & Impact

- **What changed in operations**: Three teams now work from a shared, automated data layer — no more parallel spreadsheet maintenance or manual data reconciliation between teams
- **What changed in decisions**: Pacing issues and VAST error spikes surface same-day via Slack — allowing the Ad Ops team to intervene within the same business day rather than discovering issues post-campaign
- **Content-level revenue visibility**: For the first time, revenue could be attributed to specific content (via Media ID derived from content name), enabling content-performance and monetization analysis
- **Reliable historical data**: 14-day Sunday refresh corrects late-arriving attribution — ensuring weekly revenue reports reflect final settled figures rather than provisional daily numbers

## Tech Stack

- **Ingestion**: Google Ad Manager API, Python
- **Storage**: AWS S3 (raw), Databricks Delta Lake (staging)
- **Processing**: PySpark, SQL
- **Alerting**: Slack API (pacing and error alerts)
- **Reporting**: Power BI
- **Orchestration**: Databricks Jobs (daily + weekly schedules)

## Reusable Pattern

This Detect–Alert–Act pipeline pattern — daily ingestion → derived measure standardization → threshold-based alerting — is applicable beyond ad operations:

- **E-commerce**: Promotion and conversion funnel observability with revenue pacing alerts
- **Logistics**: Delivery commitment tracking with same-day anomaly notifications
- **SaaS**: Product usage to revenue conversion monitoring with churn-signal alerts
- **Any SLA-driven function**: Event monitoring tied to business impact thresholds and action loops

**When this pattern is NOT appropriate**: If your ad operation is small enough that manual daily review is feasible, or if your analytics platform (e.g., Looker Studio connected directly to GAM) already provides adequate visibility, a full custom pipeline adds overhead rather than value. The custom pipeline becomes essential when you need cross-source attribution (content + ad + subscription), derived business logic not available in native GA reporting, or multi-team data sharing.

---

## Related Projects

[Enterprise Data Model](data-model.md) · [Semantic Layer & KPI Framework](semantic-layer.md)

---

<div class="grid cards" style="margin-top: 3rem" markdown>

-   :material-pipe:{ .lg .middle } **Solving the same problem in your organisation?**

    ---

    If your revenue or operations team is still tracking key metrics in spreadsheets — or discovering issues after the fact rather than in time to act — a production pipeline with alerting fixes that. Happy to talk through the scope and what it would take.

    [Let's talk about a project](https://mail.google.com/mail/?view=cm&fs=1&to=saamir259@gmail.com&su=Project%20inquiry%3A%20Revenue%20ops%20pipeline%20%2F%20alerting&body=Hi%20Syed%2C%0A%0AI%20saw%20your%20ad%20pipeline%20case%20study.%20We%27re%20dealing%20with%20%5Bproblem%5D%20and%20I%27d%20like%20to%20discuss%20%5Bapproach%5D.%0A%0ATimeline%3A%20%5Bx%5D){ target=_blank rel=noopener .md-button .md-button--primary }

</div>
