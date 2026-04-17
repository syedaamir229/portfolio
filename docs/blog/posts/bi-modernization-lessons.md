---
date: 2023-02-13
authors:
  - syedaamir
categories:
  - BI & Analytics
  - Data Engineering
description: "Lessons from phased BI modernization: tool migration, data-layer alignment, and metric governance without reporting disruption."
---

# BI Modernization Lessons: How to Migrate Without Breaking Trust

BI modernization fails when it is treated as a one-step tool replacement. In practice, successful transitions are staged across tooling, data architecture, and metric governance.

<!-- more -->

This post covers what I learned coordinating a BI modernization at Shahid (MBC Group) — a project that ran from August 2022 through March 2023 for the first two phases, and continued through mid-2024 for the third. The modernization path worked because it was intentionally sequenced, not rushed.

## Starting Point: What the Reporting Estate Looked Like

Before the migration kicked off, the platform's reporting was built entirely on Tableau, connected to a mix of legacy data sources — SQL Server tables, direct connections to source systems like Youbora (video analytics), Evergent (subscriber management), and Google Ad Manager. Reports had been built over time by different teams and individuals, each with their own conventions for metric definitions, naming, and data source connections. There was no shared semantic layer. KPI logic lived inside individual report files, which meant the same metric could be calculated slightly differently depending on who built the report and when.

The reporting estate served multiple departments — content, subscriptions, ad operations, finance — and the reports were actively used for operational and executive decision-making. Any disruption during migration would have been immediately visible and damaging to trust in the analytics function.

## Phase Order That Reduced Risk

The migration followed three sequential phases:

1. Move report consumers to the target BI tool (Tableau to Power BI) with continuity against legacy data sources.
2. Repoint reports to governed shared data assets (legacy sources to the Databricks Gold layer).
3. Centralize KPI logic in a semantic model (report-level DAX to shared SSAS measures).

Each phase solved a different category of risk:

- **Usability risk** — phase 1 ensured report consumers could work in the new tool before anything else changed underneath them.
- **Data consistency risk** — phase 2 ensured reports pointed at a governed, modeled data layer rather than raw or legacy sources.
- **Metric drift risk** — phase 3 ensured KPI definitions lived in one place instead of being scattered across individual report files.

The key decision was to run these as three separate, sequential phases rather than attempting a simultaneous migration. The reasoning: migrating tool, data layer, and metric logic at the same time introduces compounding failure modes. If something breaks during a simultaneous swap, you cannot isolate whether the issue is the new BI tool, the new data architecture, or the new metric definitions. Separating the phases meant each one could be independently tested and validated before moving on. If a phase introduced a problem, the blast radius was contained to one layer. It also meant reporting teams only had to absorb one change at a time, which made training and enablement manageable.

The slower timeline was a deliberate trade-off. A single big-bang migration might have been faster on paper, but the risk of cascading failures and stakeholder fatigue made the phased approach the better bet for an estate of this size.

## What Teams Usually Underestimate

**Migration overlap with other architecture changes.** In this case, the Data Model 2.0 rollout was happening simultaneously with the BI migration. The data engineering team was rebuilding the underlying data architecture at the same time that the BI team needed stable data sources to migrate reports onto. This created a coordination challenge: phase 2 of the BI migration (repointing reports to the new data layer) could not start until the relevant Gold-layer tables were available and validated in Data Model 2.0. In practice, this meant close sequencing between the data engineering and BI workstreams, and accepting that some reports would temporarily remain on legacy sources longer than planned. Without explicit coordination here, there is a real risk of double-rework — migrating a report to a data source that itself is about to change.

**Support load during cutover.** Every phase generated a spike in questions, minor issues, and requests for help from report owners. Report owners had varying levels of technical proficiency, so enablement could not be a single training session. Each phase required dedicated Q&A windows and hands-on support during the transition period.

**Hidden KPI variants inside legacy files.** Legacy Tableau workbooks contained metric definitions that had drifted over time — the same KPI calculated with slightly different filters, date ranges, or aggregation logic depending on which team or individual built the report. These variants only surfaced during migration when reports were rebuilt and validated against a common source. Ignoring these creates rework and stakeholder fatigue when numbers do not match post-migration.

## Controls That Helped

**Clear ownership by phase.** Each phase had a named owner responsible for delivery, communication, and issue resolution. This avoided the diffusion of responsibility that happens when migration is treated as a shared team activity with no single point of accountability. For phases 1 and 2, I coordinated across teams; for phase 3, I had direct ownership of the semantic-layer alignment work.

**Cutover runbooks and communication windows.** Every major cutover had a written runbook: the sequence of steps to switch a report from old source to new, the validation checks to run before and after, the rollback procedure if something broke, and the communication plan for affected stakeholders. Communication windows were scheduled in advance so report consumers knew when to expect changes and who to contact if something looked wrong. This removed ambiguity during the most fragile moments of each phase.

**Pilot dashboards before broad rollout.** Rather than migrating everything at once within a phase, a small set of representative dashboards were migrated first as pilots. These pilots were validated with their primary stakeholders before the broader rollout proceeded. This caught issues early — data source mismatches, formatting differences, performance regressions — before they could affect the full estate.

**Explicit deprecation of report-local KPI logic.** In phase 3, it was not enough to build the semantic layer and hope teams would adopt it. There was an explicit deprecation process: once a measure was available in the shared SSAS model, the corresponding local DAX logic in individual report files was removed. This prevented a situation where both the old and new approaches coexisted indefinitely, which would have undermined the governance benefits of the whole exercise.

## Outcomes

By the end of the three phases:

- **35% reduction in report maintenance costs** — updates to KPI definitions now required changes in one place rather than across every report file, and automation replaced manual refresh and distribution workflows.
- **40% increase in report adoption** among business users — tailored departmental dashboards on the new platform made reporting more accessible and relevant to non-technical stakeholders.
- **100+ shared measures** consolidated into the semantic layer — eliminating duplicated metric definitions that had accumulated across individual report files over years.
- **Zero reporting disruption** across all three phases — the phased approach delivered its core promise of maintaining business continuity throughout the transformation.

Modernization success is not just shipping the new stack. It is maintaining trust while the stack changes — and leaving behind an architecture that is easier to govern, extend, and hand off.

---

*For the full case study, see [BI Modernization Roadmap](../../projects/bi-migration.md).*

<div class="grid cards" style="margin-top: 3rem" markdown>

-   :material-swap-horizontal:{ .lg .middle } **Solving the same problem in your organisation?**

    ---

    BI migrations fail when tool changes, data layer changes, and metric governance changes happen simultaneously without a clear sequencing strategy. If you're planning a platform modernization and want to avoid the common failure modes, happy to share what worked and what to watch out for.

    [Let's Talk](https://mail.google.com/mail/?view=cm&fs=1&to=saamir259@gmail.com&su=Project%20inquiry%3A%20BI%20modernization%20%2F%20migration&body=Hi%20Syed%2C%0A%0AI%20saw%20your%20BI%20Modernization%20case%20study.%20We%27re%20dealing%20with%20%5Bproblem%5D%20and%20I%27d%20like%20to%20discuss%20%5Bapproach%5D.%0A%0ATimeline%3A%20%5Bx%5D){ target=_blank rel=noopener .md-button .md-button--primary }

</div>
