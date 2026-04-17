---
title: Viewing Behavior Clustering
description: Built behavior-first user segmentation that enabled actionable audience groups for content strategy and targeted campaigns.
tags:
  - Data Science
hide:
  - navigation
---

# Viewing Behavior Clustering

!!! abstract "Case Study Summary"
    **Organization**: Shahid (MBC Group)
    **Role**: Data Science & Advanced Analytics
    **Timeline**: 2024
    **Industry**: Media & Entertainment (Data Science)
    **Ownership**: Key contributor to feature selection, clustering workflow design, validation, and activation

    **Impact Metrics**:

    - Built **K-means clustering model** using combined viewing behavior tags and content semantic embeddings
    - Identified **12 distinct audience segments** with clear behavioral and content preference patterns
    - Scored **7.5M active profiles** across the platform
    - Dual-signal approach: engagement tag matrix (viewing hours by mood, sub-genre, dialect) combined with semantic synopsis embeddings from content descriptions
    - Clusters integrated into the **Semantic Layer** and used by Jarvis CRM automation for targeted recommendations
    - Segments include: Platform Flagships, Social Dramas, Kids, GCC Social & Lighthearted, Sports & Current Affairs, Egyptian Comedies, and more

Audience segmentation was broad before this work. Teams relied on simple dimensions such as plan type or geography, which did not capture real user behavior patterns.

## Challenge

- **Low segmentation fidelity**: Existing groups did not reflect viewing behavior
- **Limited activation value**: Broad segments were difficult to convert into campaign actions
- **Content planning blind spots**: Teams lacked behavior-led audience profiles
- **Validation need**: Segments had to make business sense, not just statistical sense

## Approach

- Selected and prepared profile-level behavioral features from the shared feature store
- Applied unsupervised clustering and evaluated cluster quality across multiple runs
- Refined feature inputs and segment definitions until clusters were stable and interpretable
- Mapped clusters into business-friendly segment profiles for activation
- Published segment outputs for campaign targeting and content strategy use

## Results & Impact

- Teams gained behavior-based segments that were practical for day-to-day decisions
- Campaign audiences became more targeted and less generic
- Content planning discussions used clearer audience profiles
- Segmentation outputs fed downstream personalization and analytics workflows

## Tech Stack

- Python (scikit-learn, pandas, numpy)
- Databricks
- PySpark
- Power BI

## Reusable Pattern

Behavior-first clustering is relevant across many domains:

- **E-commerce**: Browsing and purchase behavior segments
- **Fintech**: Transaction pattern segments for product fit and risk review
- **Gaming**: Player archetypes for retention and progression strategies
- **SaaS**: Product usage segments for onboarding and expansion motions

## Additional Context

- Delivered as one of the first advanced analytics projects after core BI foundation work
- Segment outputs were designed for activation, not only analysis

<div class="grid cards" style="margin-top: 3rem" markdown>

-   :material-handshake:{ .lg .middle } Let's connect!

    ---

    Interested in discussing a data or AI project? Reach out via LinkedIn or email.

    [LinkedIn :material-arrow-top-right:](https://www.linkedin.com/in/syedaamiruddin/){ .md-button .md-button--primary target=_blank rel=noopener }
    [Let's talk about a project](https://mail.google.com/mail/?view=cm&fs=1&to=saamir259@gmail.com&su=Let%27s%20work%20together){ target=_blank rel=noopener .md-button }

</div>
