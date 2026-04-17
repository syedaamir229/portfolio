---
date: 2025-05-19
authors:
  - syedaamir
categories:
  - AI & Automation
  - Data Engineering
description: "How a scenario-based automation platform replaced manual CRM workflows, enabling self-service campaign creation and closed-loop performance tracking."
---

# AI-Powered CRM Automation: Bridging Data Science and Marketing

Most CRM teams run into the same bottleneck: they know what audiences they want to target, but they depend on data teams to build those audiences every time. This post walks through how a campaign automation platform put targeting power back in the hands of CRM operators.

<!-- more -->

## The Problem

At Shahid (MBC Group), CRM campaign setup was a multi-step handoff process. A campaign manager would request a specific audience segment from the data team. An analyst would write SQL, validate the output, and push the list to the CRM platform. This cycle repeated for every campaign, every variation, every schedule.

The issues were predictable:

- **Slow turnaround**: Each campaign required analyst involvement, even for recurring scenarios
- **Inconsistent targeting**: Without reusable logic, similar campaigns could use different rules
- **No feedback loop**: Campaign setup and campaign results lived in separate workflows
- **Scaling limits**: As the number of campaigns grew, the manual process could not keep up

## The Approach

Rather than building a self-service UI from scratch, the focus was on what would create the most leverage: a **scenario engine** that encoded targeting logic into reusable, composable rules.

### 1. Data Foundation

The platform sits on top of an existing enterprise data model with Gold-layer feature tables:

- **Subscriber features**: Tenure, churn risk, content preferences, engagement cohorts
- **Cluster assignments**: Viewing behavior segments from prior clustering work
- **Behavior signals**: Recent activity, title exposure, session patterns
- **Subscription lifecycle**: Movement tracking (new, churned, reactivated, upgraded)

This was critical. The automation layer did not need its own data pipeline. It consumed what was already clean and governed.

### 2. Scenario Engine

The core of the platform is a scenario engine that runs four distinct recommendation scenarios in parallel, each targeting a different user behavior:

- **Clustered Top Titles**: Recommends popular shows among subscribers with similar viewing preferences, matched by behavior cluster, predicted gender, and region. This is collaborative filtering: "people like you watched these."
- **Episodes Remaining**: Re-engages subscribers who started a series but did not finish it. The system identifies titles where the subscriber has watched more than two episodes but still has episodes remaining, within a configurable recency window.
- **Ranked Up Titles**: Surfaces content that is climbing in regional popularity week over week. It targets the "what is everyone watching right now" impulse by highlighting titles that moved up in rank but are not yet in the top five.
- **AVOD Top Titles**: Promotes the most popular ad-supported content per region for non-paying users, ranked by unique viewers with meaningful watch time.

Each scenario also applies standard filters:

- **Deduplication**: Already-watched content is excluded, and content sent in the last 60 days is not re-sent
- **Content guards**: Kids content, sports content, and inactive catalog titles are filtered out automatically
- **Eligibility checks**: Only adult, active profiles are processed

Scenarios are composable by design. A CRM manager can adjust parameters (recency windows, engagement thresholds, candidate pool sizes) without writing SQL, and the platform handles the filtering and ranking logic.

### 3. Profile-to-Account Resolution

One design challenge was specific to how the CRM platform worked. The delivery channel (CleverTap) targets at the account level, but a single household can have multiple viewing profiles. Recommending content based on the account would lose personalization in multi-profile households.

The solution: process recommendations at the profile level for accuracy, then select one profile per account for delivery. The selection method is configurable -- default is the primary profile (highest watch hours), but it can be switched to most recent activity or dominant profile depending on the campaign goal.

### 4. Behavior-Based Prioritization

When an account has recommendations from multiple scenarios, the system selects one. The default mode uses recency of activity:

- Subscribers active in the last 7 days get **Episodes Remaining** (re-engagement works when the habit is fresh)
- Subscribers active 8-30 days ago get **Ranked Up Titles** (trending content to reignite interest)
- Subscribers inactive for more than 30 days get **Clustered Top Titles** (discovery via similar profiles)
- Non-paying users always get **AVOD Top Titles**

This behavior-based prioritization replaced a fixed rotation and improved targeting relevance without additional manual configuration.

### 5. CRM Integration

Scenario outputs push directly to **CleverTap** via API. This means:

- Audiences are pushed on schedule, not manually
- CRM managers configure activation timing within CleverTap
- Push, email, and SMS channels are all supported through the same audience push
- A temporal configuration system handles seasonal overrides (e.g., filtering to region-appropriate content during Ramadan) with auto-activation dates, requiring no code changes

### 6. Closed-Loop Performance

Campaign results flow back from CleverTap into the analytics layer. This creates a feedback loop where the team can:

- Compare engagement rates across different scenario configurations
- Identify which targeting rules drive the best outcomes
- Refine scenarios over time based on actual performance data

The sent-content tracking also feeds back into deduplication, ensuring the same subscriber does not receive the same recommendation repeatedly within the lookback window.

## What Made It Work

A few design decisions that mattered:

**Build on existing data, not new pipelines.** The scenario engine consumes the same Gold-layer tables that power BI and ML workflows. No duplicate data, no sync issues.

**Encode domain knowledge, not just data.** The rules in the scenario engine reflect real business logic that CRM managers already use: cluster definitions, recency windows, eligibility criteria. This is not generic ML. It is structured automation of expert knowledge.

**Keep the feedback loop tight.** By connecting campaign setup and campaign results in the same analytics flow, the team can iterate on scenarios with evidence rather than intuition.

**Make configuration declarative.** Parameters like deduplication windows, engagement thresholds, and seasonal overrides are config values, not code. This means the CRM team can adjust campaign behavior without engineering involvement.

## The Transferable Pattern

This decision-driven automation pattern is not specific to streaming or CRM. The same architecture works wherever there is:

- A data foundation with user-level features
- Repeatable targeting or segmentation decisions
- An activation channel that accepts audience pushes
- A need for performance tracking

Common applications:

- **E-commerce**: Promotion targeting, cart recovery, lifecycle messaging
- **SaaS**: Onboarding sequences, upsell campaigns, churn prevention
- **Fintech**: Product nudges, risk communications, re-engagement flows
- **Telecom**: Upgrade campaigns, retention offers, usage-based messaging

## Key Takeaway

The most impactful automation is not always the most technically complex. In this case, the value came from encoding existing business knowledge into a structured, reusable system and connecting it end-to-end from data to activation to measurement.

The CRM team went from requesting audiences to configuring scenarios. The data team went from running ad-hoc queries to maintaining a governed automation layer. Both sides benefited.

---

*For the full case study, see [CRM Campaign Automation Platform](../../projects/jarvis.md).*

<div class="grid cards" style="margin-top: 3rem" markdown>

-   :material-robot:{ .lg .middle } **Exploring CRM automation for your team?**

    ---

    Read the full case study for the technical details, or reach out if you want to talk through what this could look like for your stack.

    [Let's Talk](https://mail.google.com/mail/?view=cm&fs=1&to=saamir259@gmail.com&su=Let%27s%20work%20together){ target=_blank rel=noopener .md-button .md-button--primary }
    [Full Case Study :material-arrow-right:](../../projects/jarvis.md){ .md-button }

</div>
