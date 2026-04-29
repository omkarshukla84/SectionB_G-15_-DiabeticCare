# Diabetic Patient Readmission Analytics

> **Newton School of Technology · Data Visualization & Analytics · DVA Capstone 2**

![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square&logo=python) ![Tableau](https://img.shields.io/badge/Tableau-Public-orange?style=flat-square&logo=tableau) ![Dataset](https://img.shields.io/badge/Dataset-101%2C766%20Encounters-green?style=flat-square) ![Sector](https://img.shields.io/badge/Sector-Healthcare-red?style=flat-square) ![Status](https://img.shields.io/badge/Status-Submitted-brightgreen?style=flat-square)

Identifying clinical, demographic, and operational drivers of 30-day hospital readmissions among diabetic inpatients across 130 US hospitals (1999–2008) using Python and Tableau.

---

## Project Overview

| Field | Details |
|---|---|
| **Project Title** | Diabetic Patient Readmission Analytics |
| **Sector** | Healthcare |
| **Team ID** | G-15 |
| **Section** | Section B |
| **Faculty Mentor** | _To be filled_ |
| **Institute** | Newton School of Technology |
| **Submission Date** | April 2026 |
| **Live Dashboard** | [Tableau Public — G-15 Diabetic Care](https://public.tableau.com/app/profile/shobhit.real/viz/G-15_DiabeticCare_17773552740700/Dashboard1) |

---

## Team

| Role | Name | GitHub |
|---|---|---|
| Project Lead | _To be filled_ | — |
| Data Lead | _To be filled_ | — |
| ETL Lead | Jatin Verma | `jatinverma2007` |
| Analysis Lead | Archit Raj | `github-handle` |
| Visualization Lead | Shobhit | `github-handle` |
| Strategy Lead | Om Kar Shukla | `omkarshukla84` |
| PPT & Quality Lead | Rishav Dewan | `rishavdewan10` |

---

## Business Problem

The CMS Hospital Readmissions Reduction Program (HRRP) penalises hospitals for excess 30-day readmissions. Diabetic patients carry a disproportionate burden — diabetes is a chronic, multi-system condition where any lapse in discharge planning or post-discharge follow-up rapidly triggers re-hospitalisation. Across this dataset, **8.98% of 69,990 cleaned encounters** resulted in readmission within 30 days. At $14,400 per readmission (AHRQ, 2023), conservative interventions are estimated to save **$41.6M annually**.

**Core Business Question**

> Which clinical, demographic, and operational factors most strongly drive 30-day diabetic readmissions — and what targeted actions can hospital administrators take to reduce this rate?

**Decision Supported**

> Hospital administrators can redesign discharge protocols, deploy admission-point risk scoring, and allocate post-discharge resources toward the highest-risk patient segments — reducing readmissions and avoiding CMS penalties.

---

## Dataset

| Attribute | Details |
|---|---|
| **Source** | UCI Machine Learning Repository — Health Facts (Cerner Corporation) |
| **Access Link** | [UCI Diabetic 130-US Hospitals Dataset](https://archive.ics.uci.edu/ml/datasets/diabetes+130-us+hospitals+for+years+1999-2008) |
| **Raw Records** | 101,766 encounters |
| **Cleaned Records** | 69,990 (after deduplication, null removal, deceased/hospice exclusion) |
| **Columns** | 50 (clinical, demographic, operational) |
| **Period** | 1999–2008 · 130 US hospitals |
| **Target Variable** | `readmitted_30day` — binary: Yes (`<30` days) / No (otherwise) |
| **Positive Class Rate** | **8.98%** (6,285 patients) |
| **Format** | CSV |

**Key Columns Used**

| Column | Type | Description | Role in Analysis |
|---|---|---|---|
| `readmitted` | String | `<30` / `>30` / `NO` | Source of binary target variable |
| `age` | String | Age bracket e.g. `[70-80)` | KPI 5, risk segmentation, dashboard filter |
| `admission_type_id` | String | Emergency / Elective / Urgent | KPI 3, dashboard filter |
| `number_inpatient` | Int | Prior inpatient visits (past year) | Strongest single predictor — KPI 6, risk score |
| `num_medications` | Int | Medications during encounter | Polypharmacy indicator — KPI 8 |
| `num_lab_procedures` | Int | Lab tests during encounter | Clinical intensity — KPI 8 |
| `number_diagnoses` | Int | Diagnoses per encounter | Comorbidity measure — KPI 10 |
| `change` | String | `Ch` = changed / `No` = unchanged at discharge | Discharge quality indicator — KPI 4 |
| `insulin` | String | Up / Down / Steady / No | Insulin management flag — KPI 7 |
| `diabetesMed` | String | Yes / No | Medication coverage — KPI 9 |

For full column definitions and derived features, see [`docs/data_dictionary.md`](docs/data_dictionary.md).

---

## Data Cleaning & ETL

5-stage Python pipeline — [`notebooks/02_cleaning.ipynb`](notebooks/02_cleaning.ipynb)

| Stage | Action | Result |
|---|---|---|
| **1 — Ingestion** | Load raw CSV via `pandas.read_csv()`; identify `?` as null placeholder | 101,766 × 50 loaded |
| **2 — Missing Values** | `weight` dropped (96.9% null); `medical_specialty` → `Unknown`; `race` rows removed (2.2%) | Key nulls resolved |
| **3 — Deduplication** | First encounter per patient retained; deceased/hospice discharges removed (IDs 11,13,14,19,20,21) | 69,990 clean records |
| **4 — Feature Engineering** | Binary `readmitted_30day` target; `admission_type_id` mapped to labels; `on_insulin` flag; 4-variable risk score | Model-ready features |
| **5 — Export** | `data/processed/diabetic_clean.csv` with validated column types | Ready for Tableau ingestion |

---

## KPI Framework

All 10 KPIs computed in [`notebooks/05_final_load_prep.ipynb`](notebooks/05_final_load_prep.ipynb) and verified against the live Tableau dashboard.

| # | KPI | Formula | Dashboard Value |
|---|---|---|---|
| 1 | **30-Day Readmission Rate** | `COUNT(readmitted='<30') / COUNT(total) × 100` | **8.98%** |
| 2 | **Overall Readmission Rate** | `COUNT(readmitted≠'NO') / COUNT(total) × 100` | **46.1%** (raw dataset) |
| 3 | **Emergency Admission Rate** | `COUNT(admission_type_id=1) / COUNT(total) × 100` by readmitted group | Emergency = largest readmitted segment |
| 4 | **Medication Change Rate at Discharge** | `COUNT(change='Ch') / COUNT(total) × 100` by readmitted | **52.70% of readmits — NO change** |
| 5 | **High-Risk Age Group Share** | `COUNT(<30 by age) / COUNT(all <30) × 100` | **[70-80] highest — 17,751 encounters** |
| 6 | **Prior Inpatient Visit Rate** | `AVG(number_inpatient)` by readmitted group | **0.35 (readmitted) vs 0.12 (not) — 3× gap** |
| 7 | **Insulin Usage Rate** | `COUNT(insulin≠'No') / COUNT(total) × 100` by readmitted | **58.1% of early readmits on insulin** |
| 8 | **Avg Lab Procedures per Encounter** | `AVG(num_lab_procedures)` by readmitted | Higher for readmitted cohort |
| 9 | **Diabetes Medication Prescription Rate** | `COUNT(diabetesMed='Yes') / COUNT(total) × 100` by readmitted | High prescription rate, outcomes not improving |
| 10 | **Avg Diagnoses per Encounter** | `AVG(number_diagnoses)` by readmitted | **7.69 (readmitted) vs 7.22 (not readmitted)** |

---

## Tableau Dashboard

**Live URL:** [G-15 Diabetic Care Dashboard](https://public.tableau.com/app/profile/shobhit.real/viz/G-15_DiabeticCare_17773552740700/Dashboard1)

| View | Content | Filters |
|---|---|---|
| **Dashboard 1 — Executive Overview** | KPI cards (8.98% rate · avg prior visits 0.18 · avg medications 15.67) · Readmission outcome pie (6,285 vs 63,705) · Age group bar · Admission type stacked bar | Age Bracket · Readmission Outcome · Admission Type |
| **Dashboard 2 — Clinical Deep Dive** | Clinical intensity scatter (lab procedures vs medications) · Prior inpatient visits bar (0.35 vs 0.12) · Medication change grouped bar (52.70% no-change for readmitted) | Admission Type · Readmission Outcome |
| **Dashboard 3 — Risk Segmentation** | Risk distribution donut (Low / Medium / High) · All 6 clinical indicators higher for readmitted · KPI cards: High Risk 2.35% · Avg Risk Score 2.16 | Patient Risk Segment · Measure Names |

Screenshots: [`tableau/screenshots/`](tableau/screenshots/) — Link file: [`tableau/dashboard_links.md`](tableau/dashboard_links.md)

---

## Key Insights

1. **8.98% readmission rate is a systemic discharge failure, not patient non-compliance.** 6,285 of 69,990 patients returned within 30 days — hospital administration must own this, not pass it to patients.
2. **The [70-80] age bracket drives 17,751 encounters — more than double the dataset average.** Standard discharge protocols built for younger patients are failing this cohort structurally.
3. **Prior inpatient history is the most actionable admission-point predictor.** Readmitted patients averaged 0.35 prior visits vs 0.12 for non-readmitted — a 3× gap available the moment a patient checks in.
4. **52.70% of readmitted patients were discharged without any medication change.** That is 3,300+ patients per year leaving without treatment adjustment after a diabetic hospitalisation.
5. **Emergency is the dominant admission pathway for the readmitted cohort.** Emergency intake must be treated as a critical intervention point, not just an intake channel.
6. **High Risk patients are only 2.35% of the population but score highest on all 6 clinical indicators simultaneously.** Small cohort, maximum resource consumption.
7. **All 6 clinical indicators are uniformly elevated for readmitted patients.** `num_lab_procedures`, `num_medications`, `number_inpatient`, `time_in_hospital`, `number_outpatient`, `number_emergency` — every metric higher without exception.
8. **Average 15.67 medications per encounter — polypharmacy is the baseline, not the exception.** Coordination failure at discharge compounds across every one of those medications.
9. **Medium Risk patients represent the highest-leverage intervention target.** Disproportionately high readmission conversion rate relative to cohort size — best return on intervention effort.
10. **Longer inpatient stays are not preventing 30-day returns.** Time in hospital is higher for readmitted patients — the failure is in discharge execution quality, not inpatient care intensity.

---

## Recommendations

| # | Evidence | Recommendation | Expected Impact |
|---|---|---|---|
| **R1** | 52.70% of early readmits discharged without medication change | **Mandate Medication Review Before Every Diabetic Discharge.** Documented endocrinologist or pharmacist sign-off required for all diabetic inpatients. Undocumented exceptions must have clinical justification logged. | ~580 readmissions prevented · **$8.3M/yr** · 60–90 days |
| **R2** | [70-80] = 17,751 encounters; 60+ accounts for majority of all early readmissions | **Launch a 14-Day Geriatric Diabetes Discharge Protocol for Patients Aged 60+.** Day 2 phone call → Day 5 nurse visit → Day 10 telehealth → Day 14 outpatient appointment. | ~1,194 readmissions prevented · **$17.2M/yr** · 90–120 days |
| **R3** | Prior visits 3× higher; elevated labs, meds, and diagnoses all measurable at Day 1 | **Deploy a 4-Variable Readmission Risk Score at Admission.** Inputs: prior inpatient visits + diagnoses + medications + lab procedures. Top 20% trigger enhanced care pathway with dedicated case manager from Day 1. | ~460 readmissions prevented · **$6.6M/yr** · 90–180 days |
| **R4** | Emergency carries the largest readmitted segment; Internal Medicine = 1,646 early readmissions | **Introduce 5-Point Specialty Discharge Checklists for Internal Medicine and Emergency.** Glucose stable 24h · patient education complete · follow-up booked · medication reconciliation signed · written care plan issued. | ~270 readmissions prevented · **$3.9M/yr** · 30–60 days |
| **R5** | 58.1% of early readmits on insulin; significant management variability across departments | **Standardise Inpatient Insulin Management Per ADA 2024 Guidelines.** Mandatory HbA1c on admission · standard titration protocol · diabetes nurse review within 24h · structured insulin education before discharge. | ~390 readmissions prevented · **$5.6M/yr** · 60–90 days |

**Combined conservative impact: ~2,894 readmissions prevented · $41.6M saved annually · fully implemented within 6 months.**

> Financial basis: $14,400 average cost per 30-day readmission (AHRQ, 2023). Impact percentages derived from peer-reviewed healthcare literature.

---

## Limitations & Future Scope

**Dataset Limitations**

| Limitation | Detail |
|---|---|
| Temporal Gap | Data covers 1999–2008. Clinical protocols have evolved significantly — validate against modern datasets before operational deployment. |
| Missing Specialty Data | ~51% of encounters have no `medical_specialty` value recorded, limiting specialty-level analysis depth. |
| Missing BMI Data | `weight` column 96.9% empty — BMI-based diabetic risk analysis not possible. |
| No Post-Discharge Outcomes | Dataset records whether readmission occurred, not why — no mortality, reason, or patient outcome data. |
| US-Specific Scope | 130 US hospitals only. Findings may not generalise to other healthcare systems or insurance structures. |
| Class Imbalance | 8.98% positive class — any ML extension requires balancing techniques to avoid false negatives. |

**Future Scope**

- **ML Risk Classifier** — Logistic regression or random forest using the 4-variable risk profile to flag high-risk patients at admission in real time.
- **Live EHR Integration** — Connect Tableau to a live hospital EHR for real-time readmission risk monitoring and automated nursing alerts.
- **3-Year ROI Model** — Full cost-benefit analysis per recommendation: implementation costs + staff training + phased readmission reduction over 36 months.
- **Patient Satisfaction Linkage** — Merge with HCAHPS scores to explore the readmission-patient experience correlation.

---

## Repository Structure

```
SectionB_G-15_DiabeticCare/
│
├── README.md
│
├── data/
│   ├── raw/                          # Original dataset — never edited
│   └── processed/                    # Cleaned CSV output from ETL pipeline
│
├── notebooks/
│   ├── 01_extraction.ipynb
│   ├── 02_cleaning.ipynb
│   ├── 03_eda.ipynb
│   ├── 04_statistical_analysis.ipynb
│   └── 05_final_load_prep.ipynb
│
├── scripts/
│   └── etl_pipeline.py
│
├── tableau/
│   ├── screenshots/
│   └── dashboard_links.md
│
├── reports/
│   ├── README.md
│   ├── project_report_template.md
│   └── presentation_outline.md
│
├── docs/
│   └── data_dictionary.md
│
├── DVA-oriented-Resume/
└── DVA-focused-Portfolio/
```

---

## Tech Stack

| Tool | Status | Purpose |
|---|---|---|
| Python 3.10+ | Mandatory | ETL, cleaning, analysis, KPI computation |
| Jupyter Notebooks | Mandatory | Reproducible analysis pipeline |
| Google Colab | Supported | Cloud notebook execution |
| Tableau Public | Mandatory | Dashboard design, publishing, sharing |
| GitHub | Mandatory | Version control, collaboration, contribution audit |
| SQL | Optional | Initial data extraction only |

**Python libraries:** `pandas` · `numpy` · `matplotlib` · `seaborn` · `scipy` · `statsmodels`

---

## Submission Checklist

**GitHub Repository**
- [x] Public repository with correct naming convention
- [ ] All notebooks committed in `.ipynb` format
- [ ] `data/raw/` contains original unedited dataset
- [ ] `data/processed/` contains cleaned pipeline output
- [ ] `tableau/screenshots/` contains dashboard screenshots
- [x] `tableau/dashboard_links.md` contains Tableau Public URL
- [x] `docs/data_dictionary.md` complete
- [x] `README.md` explains project, dataset, and team
- [ ] All members have visible commits and pull requests

**Tableau Dashboard**
- [x] Published on Tableau Public with working public URL
- [x] Minimum 2 interactive filters applied across all worksheets
- [x] Dashboard directly addresses the business problem

**Project Report**
- [x] Exported as PDF into `reports/`
- [x] Cover page, executive summary, sector context, problem statement
- [x] Data description, cleaning methodology, KPI framework
- [x] EDA with written insights and statistical analysis results
- [x] Dashboard screenshots and explanation
- [x] 8–12 key insights in decision language
- [x] 3–5 actionable recommendations with impact estimates
- [x] Contribution matrix matches GitHub history

**Presentation Deck**
- [ ] Exported as PDF into `reports/`
- [ ] Title slide through recommendations, impact, limitations, and next steps

**Individual Assets**
- [x] DVA-oriented resume updated to include this capstone
- [x] Portfolio link or project case study added

---

## Contribution Matrix

| Team Member | Dataset & Sourcing | ETL & Cleaning | EDA & Analysis | Statistical Analysis | Tableau Dashboard | Report Writing | PPT & Viva |
|---|---|---|---|---|---|---|---|
| Rishav Dewan | Support | Support | Support | Support | Support | **Owner** | Support |
| Jatin Verma | **Owner** | **Owner** | Support | Support | Support | Support | Support |
| Archit Raj | Support | Support | **Owner** | **Owner** | Support | Support | Support |
| Shobhit | Support | Support | Support | Support | **Owner** | Support | Support |
| Om Kar Shukla | Support | Support | Support | Support | Support | Support | **Owner** |
| Rachit Singh | Support | **Owner** | Support | Support | Support | **Owner** | Support |

_We confirm the above is accurate and verifiable through GitHub Insights, PR history, and submitted artifacts._

**Team Lead:** _____________________________ &nbsp;&nbsp;&nbsp; **Date:** _______________

---

## Academic Integrity

All analysis, code, and recommendations in this repository are the original work of Group G-15. Free-riding is tracked via GitHub Insights and pull request history. Any mismatch between the contribution matrix and actual commit history may result in individual grade adjustments.

---

<div align="center">

**Newton School of Technology — Data Visualization & Analytics · Capstone 2**

*"Data reveals the problem. Decisions fix it."*

</div>
