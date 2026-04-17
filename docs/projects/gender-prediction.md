---
title: Behavior-Based Attribute Inference
description: Built a behavioral inference model to enrich incomplete profile attributes and improve downstream audience analysis.
tags:
  - Data Science
hide:
  - navigation
---

# Behavior-Based Attribute Inference

!!! abstract "Case Study Summary"
    **Organization**: Shahid (MBC Group)
    **Role**: Data Science & Advanced Analytics
    **Timeline**: July – October 2025
    **Industry**: Media & Entertainment (Data Science)
    **Ownership**: End-to-end owner of feature engineering, model training, validation, and deployment handoff

    **Impact Metrics**:

    - Built an **XGBoost classification model** predicting user gender from viewing behavior patterns
    - Achieved **75% accuracy** and **0.81 AUC** on a validation set of 700K single-adult-profile accounts
    - Scored **9.5M adult profiles** — expanding demographic coverage from 22.7% (self-reported only) to **100% of engaged accounts**
    - **5.8M new predictions** generated for profiles with no prior gender data
    - Top predictive features: protagonist gender (16.6%), audience affinity (9.3%), and sub-genre preferences (5.5%)
    - Model operates at **profile level** (not account level), enabling household-aware personalization

Declared profile attributes were incomplete for a meaningful portion of users. A behavior-based inference workflow was needed to improve usable audience context.

## Challenge

- **Missing profile fields**: Key attributes were incomplete for many users
- **Data quality variance**: Declared values were not always reliable in shared-account scenarios
- **Planning limitations**: Content and marketing teams needed broader audience context
- **Activation requirement**: Inferred signals had to be usable in existing targeting workflows

## Approach

- Engineered behavioral features from profile activity and content interaction patterns
- Trained and compared classification models using validated training subsets
- Evaluated model behavior across segments to ensure practical consistency
- Published inferred attributes as controlled downstream features for analytics and targeting use

## Results & Impact

- Audience analysis became more complete where declared attributes were sparse
- Teams gained additional context for planning and campaign design
- Behavioral signals were converted into a practical enrichment layer
- Outputs complemented clustering and other audience modeling workflows

## Tech Stack

- XGBoost, scikit-learn
- PySpark
- Databricks, Delta Lake
- MLflow (Unity Catalog model registry)
- SQL

## Reusable Pattern

This enrichment pattern is relevant when customer profiles are incomplete:

- **E-commerce**: Attribute enrichment for personalization and targeting
- **Fintech**: Behavior-based profile completion for segmentation support
- **Advertising**: Audience enrichment for campaign planning
- **Healthcare engagement**: Behavioral enrichment for communication strategies

## Additional Context

- Built in parallel with clustering initiatives to improve audience understanding depth
- Deployed with conservative usage guidance for downstream consumers

<div class="grid cards" style="margin-top: 3rem" markdown>

-   :material-handshake:{ .lg .middle } Let's connect!

    ---

    Interested in discussing a data or AI project? Reach out via LinkedIn or email.

    [LinkedIn :material-arrow-top-right:](https://www.linkedin.com/in/syedaamiruddin/){ .md-button .md-button--primary target=_blank rel=noopener }
    [Let's talk about a project](https://mail.google.com/mail/?view=cm&fs=1&to=saamir259@gmail.com&su=Let%27s%20work%20together){ target=_blank rel=noopener .md-button }

</div>
