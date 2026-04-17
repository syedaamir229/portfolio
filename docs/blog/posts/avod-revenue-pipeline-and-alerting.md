---
date: 2023-11-20
authors:
  - syedaamir
categories:
  - Data Engineering
  - BI & Analytics
description: "How an AVOD revenue operations pipeline connected ad signals, monitoring, and alerts to replace spreadsheet-based tracking."
---

# AVOD Revenue Pipeline and Alerting: From Manual Tracking to Operating System

AVOD reporting becomes fragile when requests, impressions, pacing, and errors are tracked in disconnected files. The fix is to build one monitored pipeline that connects operational signals to business outcomes.

<!-- more -->

This system replaced spreadsheet-heavy tracking with a repeatable event-to-revenue workflow at Shahid (MBC Group). The initial build started in November 2023, covering inventory and impressions. A second phase through 2024 added delivery pacing, VAST error categorization, and Slack-based alerting. What follows is not the full case study but the design thinking and lessons that came out of building it.

## Before: Fragmented Revenue Operations

Three teams — Ad Ops, Growth, and the Data & Numbers team — all needed ad revenue data. Each was pulling exports from Google Ad Manager independently, building their own spreadsheets, and arriving at slightly different totals. There was no production pipeline for any of the four data domains: inventory, impressions, pacing, or errors. Pacing shortfalls were discovered during post-campaign reviews. VAST error spikes went unnoticed until someone happened to check. The team could report history, but could not reliably operate in near-real-time cycles.

## The Design Decision That Shaped Everything

The first real architectural question was whether to build separate pipelines per team or a shared data layer. Three teams needed the same underlying GAM data, but with different slicing and business logic applied.

We chose a unified staging layer with derived measures — a shared raw-to-staging architecture where derivation logic (ad format, ad type, VOD model, device category, content media ID via regex) is applied once at the staging layer. Every downstream consumer reads from the same derived tables.

The reasoning was practical. GAM API rate limits make it wasteful to pull the same data multiple times for different teams. More importantly, centralizing the derivation logic meant all three teams would calculate the same revenue totals. That sounds obvious, but discrepancies between teams had been a real source of friction before — different filters, different date ranges, different ways of categorizing programmatic vs. direct. One staging layer eliminated that entire class of problem.

## Four Pipelines, One System

The implementation used four linked pipelines, each covering a distinct operational signal. They needed to be separate because each has its own source structure, refresh cadence, and downstream consumers — but they also needed to share dimensions and derivation logic so cross-pipeline analysis actually works.

**Inventory** tracks sell-able ad slots: how much space is available to sell, broken down by format (instream, display), content, and country. This is the supply-side view that Ad Ops uses to understand capacity before booking campaigns.

**Impressions** covers delivered ads and the revenue they generated. This is the core commercial signal — what actually ran, what it earned, and how that breaks down across dimensions. It feeds the revenue dashboards that all three teams consume.

**VAST Errors** categorizes video ad serving failures by error type. When a video ad fails to load or render, the VAST protocol returns an error code. Categorizing and trending these errors lets the team distinguish between transient player issues and systematic delivery problems that need intervention.

**Delivery Pacing** compares actual delivery against booked commitments — the ratio of what has been delivered so far to what was promised by the end of the campaign flight. This is the pipeline that feeds alerting, because a pacing shortfall discovered on the last day of a campaign is a missed revenue opportunity.

## The 14-Day Refresh Problem

One thing that is not obvious about GAM impression data: it does not settle immediately. Late-arriving attribution means numbers can shift for up to 14 days after the original event. A daily pipeline that only looks at yesterday's data will always be slightly wrong — and the errors compound over a reporting week.

The solution was a two-schedule approach. A daily pipeline runs at 7AM to give teams fresh operational numbers for same-day decisions. Then a Sunday historical refresh re-pulls the previous 14 days to capture corrections and late-arriving data. This means the Monday morning revenue reports reflect final settled figures, not provisional daily numbers. It is a small detail, but it is the kind of thing that determines whether teams actually trust the pipeline or keep a side spreadsheet "just in case."

## Alert Thresholds: Business Impact, Not Just Technical Anomaly

The alert design was deliberately built around business impact rather than technical metrics. A technical anomaly alert might fire when error rates exceed a statistical threshold — useful, but noisy. A business-impact threshold asks a different question: will this affect revenue or campaign delivery?

For example, a delivery pacing alert does not fire because the pacing ratio dropped by some percentage from the mean. It fires when a campaign's actual delivery rate puts it on track to miss its booked commitment by the end of the flight. That is a concrete business problem — the Ad Ops team can redistribute inventory or adjust targeting while there is still time to recover. The difference is that teams act on business-impact alerts because the alert itself describes a decision, not just a data point.

VAST error alerts follow a similar logic. A brief spike in timeout errors during a CDN maintenance window is not worth paging anyone about. A sustained increase in a specific error category across a content vertical means something is broken in the ad serving chain and needs investigation.

## Why This Was a Transition Block

This project sat between BI-heavy reporting and broader analytics systems. It was not a machine learning project or a real-time streaming system. But it introduced patterns that made later, more complex systems possible:

- It required data-engineering rigor — schema normalization, idempotent pipelines, refresh strategies
- It kept BI usability requirements front and center — the output had to work in Power BI for non-technical users
- It introduced operating-model thinking — detect, alert, act — that later supported DS and AI systems built on the same data layer

The two-phase build (core pipelines in late 2023, expansion with pacing alerts and error categorization through 2024) also proved that starting with a solid staging layer makes expansion straightforward. Adding VAST Errors and Delivery Pacing in phase two was mostly about new source ingestion and alert logic — the derivation layer and dashboard infrastructure were already in place.

---

*For the full case study, see [Revenue Operations Data Pipeline & Alerting Platform](../../projects/ad-pipeline.md).*
