# EGRA Analytics Pipeline
### Early Grade Reading Assessment — Data to Dashboard

## What this is

This repository contains an end-to-end data pipeline for Early Grade Reading Assessment (EGRA) datasets — from raw KoboToolbox export to structured analysis and an interactive dashboard with an AI-powered interpretation layer.

It was built to address a gap I have observed repeatedly across humanitarian education programmes: data gets collected, reports get written, but the analysis rarely goes beyond simple averages. Disaggregation by gender and locality is either skipped or treated as an afterthought. Inferential statistics — the kind that would actually tell a programme whether an observed gap between girls in IDP camps and boys in urban schools is real or just noise — are rarely attempted in the field.

This pipeline makes rigorous analysis accessible.

---

## Field context

I have conducted and supervised EGRA assessments in some of the most difficult operating environments in the world — active conflict zones in Sudan and South Sudan, displacement settings in Kenya's northern counties, and protracted crisis contexts in Somalia. In these environments, a child's reading score is not just a data point. It reflects interrupted schooling, language of instruction mismatches, teacher absenteeism, and the particular vulnerability of girls in communities where school attendance is already contested.

That context shapes every analytical decision in this pipeline. See `case_notes/` for the reasoning behind key choices.

---

## EGRA subtasks covered

This pipeline was built around the following subtask selection from the RTI/USAID EGRA Toolkit (Second Edition):

| Subtask | Type | Duration |
|---|---|---|
| Listening Comprehension | Untimed | — |
| Letter Identification | Timed | 60 seconds |
| Word Identification | Timed | 60 seconds |
| Pseudo-Word Identification | Timed | 60 seconds |
| Reading Comprehension | Untimed (skip rule applies) | — |

The skip rule — where a child who reads zero words on Word Identification does not proceed to Reading Comprehension — is handled explicitly in the ETL pipeline. See `case_notes/01_missing_data.md`.

---

## Core disaggregation variables

Every analysis in this pipeline disaggregates by:

- **Gender** (Female / Male)
- **Locality** (Urban / Rural / IDP Camp)

This is not optional in humanitarian education programming. Donors, cluster leads, and programme teams need to see these breakdowns as a baseline before any other finding is reported. The pipeline enforces this.

---

## What the analysis produces

**Descriptive statistics**
- Mean scores and standard deviations by gender, locality, grade, and all combinations
- Zero reader rates (children who cannot read a single word) by locality and gender
- Oral reading fluency (words correct per minute) performance bands

**Inferential statistics**
- Independent samples t-tests comparing female and male scores on each subtask, with effect sizes
- One-way ANOVA comparing locality groups, with Tukey HSD post-hoc tests
- OLS regression: subtask scores as a function of gender, locality, grade, and their interactions

**AI analyst panel**
- Plain-language narrative generated from the actual statistical output
- Designed for programme staff who do not have a statistics background
- Follow-up question interface for ad hoc queries

---

## Repository structure

```
egra-dashboard/
├── pipeline/
│   ├── etl.py                  # Ingest, clean, validate, transform
│   ├── kobo_api.py             # Pull directly from KoboToolbox API
│   └── generate_synthetic.py  # Realistic synthetic data for demos
├── analysis/
│   ├── descriptive.py          # Disaggregated summary statistics
│   ├── inferential.py          # ANOVA, t-tests, OLS regression
│   └── run_analysis.py         # Orchestrator
├── agent/
│   └── analyst.py              # Claude API — AI narrative generation
├── dashboard/
│   └── app.py                  # Streamlit dashboard
├── case_notes/                 # Analytical decision log
│   ├── 01_missing_data.md
│   ├── 02_anova_design.md
│   ├── 03_regression_spec.md
│   └── 04_locality_coding.md
├── data/
│   └── synthetic/              # Demo dataset (no real child data)
└── requirements.txt
```

---

## Data privacy

No real child-level data is committed to this repository. The `data/raw/` folder is gitignored. Where a sample dataset is included, it is either fully synthetic or has been anonymised — no names, no school identifiers traceable to individuals, no geographic coordinates precise enough to identify communities.

In contexts like South Sudan and Somalia, data protection is not a compliance exercise. It is a protection obligation.

---

## Analytical decisions

The `case_notes/` folder documents the reasoning behind key methodological choices. These are written for a mixed audience — programme staff, M&E coordinators, and technically-minded donors. They explain not just *what* the pipeline does, but *why*, and what the limitations are.

---

## Tech stack

| Layer | Tools |
|---|---|
| Data ingestion | KoboToolbox API, pandas |
| Statistical analysis | scipy, statsmodels, pingouin |
| Visualisation | plotly |
| AI narrative | Anthropic Claude API |
| Dashboard | Streamlit |
| Deployment | Streamlit Community Cloud |

*For inquiries, please use the contact details provided separately.*
