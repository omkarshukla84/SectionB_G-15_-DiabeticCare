# NST DVA Capstone 2 - Project Repository
> **Newton School of Technology | Data Visualization & Analytics**
> A 2-week industry simulation capstone using Python, GitHub, and Tableau to convert raw data into actionable business intelligence.

---

## Before You Start

1. Rename the repository using the format `SectionName_TeamID_ProjectName`.
2. Fill in the project details and team table below.
3. Add the raw dataset to `data/raw/`.
4. Complete the notebooks in order from `01` to `05`.
5. Publish the final dashboard and add the public link in `tableau/dashboard_links.md`.
6. Export the final report and presentation as PDFs into `reports/`.

### Quick Start

If you are working locally:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook
```

If you are working in Google Colab:

- Upload or sync the notebooks from `notebooks/`
- Keep the final `.ipynb` files committed to GitHub
- Export any cleaned datasets into `data/processed/`

---

## Project Overview

| Field | Details |
|---|---|
| **Project Title** | Diabetic Patient Readmission Analytics |
| **Sector** | Healthcare |
| **Team ID** | G-15 |
| **Section** | Section B |
| **Faculty Mentor** | _To be filled by team_ |
| **Institute** | Newton School of Technology |
| **Submission Date** | April 2026 |

### Team Members

| Role | Name | GitHub Username |
|---|---|---|
| Project Lead | _To be filled by team_ | `github-handle` |
| Data Lead | _To be filled by team_ | `github-handle` |
| ETL Lead | Jatin Verma | `jatinverma2007` |
| Analysis Lead | Archit Raj | `github-handle` |
| Visualization Lead | Shobhit | `github-handle` |
| Strategy Lead | Rishav Dewan | `rishavdewan10` |
| PPT and Quality Lead | Om Kar Shukla | `omkarshukla84` |

---

## Business Problem

Healthcare is one of the most data-rich sectors in the world, yet it continues to suffer from preventable inefficiencies — among which hospital readmissions stand out as a critical and costly challenge. Diabetic patients carry a disproportionately high burden: diabetes is a chronic, multi-system condition requiring careful ongoing management, and any lapse in treatment continuity or post-discharge follow-up can rapidly lead to re-hospitalisation. Under the CMS Hospital Readmissions Reduction Program (HRRP), hospitals are actively penalised for excess readmissions — making this both a patient-care failure and a direct financial liability. Across 101,766 diabetic encounters from 130 US hospitals (1999–2008), 8.98% of patients (6,285) were readmitted within 30 days of discharge, at an average cost of $14,400 per event. Preventing even a fraction of these readmissions represents tens of millions of dollars in recoverable savings while directly improving patient outcomes.

**Core Business Question**

> Which clinical, demographic, and operational factors most strongly predict 30-day readmission in diabetic inpatients, and what targeted interventions can hospital administrators take to reduce this rate?

**Decision Supported**

> Hospital administrators can use this analysis to redesign discharge protocols, deploy readmission risk scoring at admission, and prioritise post-discharge follow-up resources toward the highest-risk patient segments — reducing 30-day readmission rates and avoiding CMS penalties.

---

## Dataset

| Attribute | Details |
|---|---|
| **Source Name** | UCI Machine Learning Repository / Health Facts (Cerner Corporation) |
| **Direct Access Link** | [UCI Diabetic Readmission Dataset](https://archive.ics.uci.edu/ml/datasets/diabetes+130-us+hospitals+for+years+1999-2008) |
| **Raw Row Count** | 101,766 encounters |
| **Cleaned Row Count** | 69,990 encounters (after deduplication and null removal) |
| **Column Count** | 50 columns (clinical, demographic, and operational) |
| **Time Period Covered** | 1999–2008 (10 years, 130 US hospitals) |
| **Format** | CSV |

**Key Columns Used**

| Column Name | Description | Role in Analysis |
|---|---|---|
| `readmitted` | Readmission outcome: `<30`, `>30`, or `NO` | Target variable — binary `readmitted_30day` derived from this |
| `age` | Patient age bracket (e.g. `[70-80)`) | KPI segmentation, risk stratification |
| `admission_type_id` | Admission type (Emergency, Elective, Urgent, Other) | KPI 3, dashboard filter |
| `number_inpatient` | Prior inpatient visits in the past year | Strongest single predictor — KPI 6, risk score input |
| `num_medications` | Number of medications administered during encounter | KPI 8, polypharmacy indicator |
| `num_lab_procedures` | Number of lab tests during encounter | KPI 8, clinical intensity measure |
| `number_diagnoses` | Number of diagnoses recorded per encounter | KPI 10, comorbidity measure |
| `change` | Medication change at discharge (`Ch` = changed, `No` = unchanged) | KPI 4 — discharge quality indicator |
| `insulin` | Insulin usage (Up, Down, Steady, No) | KPI 7, discharge management flag |
| `diabetesMed` | Whether diabetes medication was prescribed (`Yes`/`No`) | KPI 9 |

For full column definitions, see [`docs/data_dictionary.md`](docs/data_dictionary.md).

---

## KPI Framework

| KPI | Definition | Formula / Computation |
|---|---|---|
| **KPI 1 — 30-Day Readmission Rate** | Percentage of encounters where patient was readmitted within 30 days | `COUNT(readmitted = '<30') / COUNT(total encounters) × 100` → **8.98%** |
| **KPI 2 — Overall Readmission Rate** | Percentage of encounters where patient was readmitted at any point | `COUNT(readmitted ≠ 'NO') / COUNT(total) × 100` → **46.1% (raw)** |
| **KPI 3 — Emergency Admission Rate** | Share of admissions via Emergency, split by readmission outcome | `COUNT(admission_type_id = 1) / COUNT(total) × 100`, by readmitted group |
| **KPI 4 — Medication Change Rate at Discharge** | % of encounters where medication was adjusted before discharge | `COUNT(change = 'Ch') / COUNT(total) × 100` → **52.70% of readmits had NO change** |
| **KPI 5 — High-Risk Age Group Share** | % contribution of each age bracket to total early readmissions | `COUNT(<30 by age group) / COUNT(all <30) × 100` → **[70-80] = highest at 17,751 encounters** |
| **KPI 6 — Prior Inpatient Visit Rate** | Avg prior inpatient visits in the year before encounter, by readmission group | `AVG(number_inpatient) by readmitted` → **0.35 (readmitted) vs 0.12 (not readmitted) — ~3x gap** |
| **KPI 7 — Insulin Usage Rate** | % of encounters where insulin was prescribed, by readmission group | `COUNT(insulin ≠ 'No') / COUNT(total) × 100, by readmitted` → **58.1% of early readmits on insulin** |
| **KPI 8 — Avg Lab Procedures per Encounter** | Mean lab procedures per encounter, by readmission outcome | `AVG(num_lab_procedures) by readmitted` |
| **KPI 9 — Diabetes Medication Prescription Rate** | % of encounters where diabetes medication was prescribed | `COUNT(diabetesMed = 'Yes') / COUNT(total) × 100, by readmitted` |
| **KPI 10 — Avg Diagnoses per Encounter** | Average number of diagnoses per encounter, by readmission outcome | `AVG(number_diagnoses) by readmitted` → **7.69 (readmitted) vs 7.22 (not readmitted)** |

Document KPI logic clearly in `notebooks/04_statistical_analysis.ipynb` and `notebooks/05_final_load_prep.ipynb`.

---

## Tableau Dashboard

| Item | Details |
|---|---|
| **Dashboard URL** | [G-15 Diabetic Care Dashboard](https://public.tableau.com/app/profile/shobhit.real/viz/G-15_DiabeticCare_17773552740700/Dashboard1) |
| **Executive View (Dashboard 1)** | KPI cards (8.98% readmission rate, avg prior visits, avg medications) + Readmission outcome pie + Age group bar chart + Admission type stacked bar. Filters: Age Bracket, Readmission Outcome, Admission Type |
| **Clinical Deep Dive (Dashboard 2)** | Clinical intensity scatter (lab procedures vs medications) + Prior inpatient visits bar (0.35 vs 0.12) + Medication change grouped bar (52.70% no-change for readmitted). Filters: Admission Type, Readmission Outcome |
| **Risk Segmentation (Dashboard 3)** | Risk distribution donut (Low/Medium/High) + Clinical indicators comparison bar (all 6 metrics higher for readmitted). KPI cards: High Risk % (2.35%), Avg Risk Score (2.16). Filters: Patient Risk Segment, Measure Names |
| **Main Filters** | Age Bracket, Readmission Outcome, Admission Type, Patient Risk Segment — all applied across worksheets |

Store dashboard screenshots in [`tableau/screenshots/`](tableau/screenshots/) and document the public links in [`tableau/dashboard_links.md`](tableau/dashboard_links.md).

---

## Key Insights

1. **8.98% readmission rate is a systemic failure, not statistical noise.** 6,285 patients from 69,990 re-admitted within 30 days. Hospital administration must treat this as a discharge system failure, not a patient compliance issue.
2. **The [70-80] age group is the highest-volume cohort — by a wide margin.** 17,751 encounters in this bracket, more than double the ~8,000 average. Standard discharge protocols designed for younger patients are not serving this population.
3. **Prior inpatient history is the best admission-point predictor available.** Readmitted patients averaged 0.35 prior inpatient visits vs 0.12 for non-readmitted (~3x gap). This variable is captured at admission and can immediately trigger enhanced care routing.
4. **Over half of readmitted patients left without any medication adjustment.** 52.70% of early readmits had no medication change at discharge — 3,300+ patients annually discharged without treatment optimisation.
5. **Emergency is the dominant admission pathway for readmitted patients.** The admission type chart shows Emergency with the largest readmitted segment. Emergency intake is a critical intervention point, not merely an observation point.
6. **High Risk patients: only 2.35% of the population, but the most resource-intensive.** Avg Risk Score = 2.16 across the population, but High Risk patients score highest on all 6 clinical indicators.
7. **All 6 clinical indicators are consistently higher for readmitted patients.** Dashboard 3 confirms: `num_lab_procedures`, `num_medications`, `number_inpatient`, `time_in_hospital`, `number_outpatient`, `number_emergency` — ALL elevated for the readmitted cohort.
8. **Avg 15.67 medications per encounter — polypharmacy is endemic.** 10+ medications is the norm for this population, creating compounding coordination risk at every discharge.
9. **Medium Risk patients represent the largest preventable readmission opportunity.** Medium Risk Yes count is disproportionately high relative to cohort size — best volume-to-effort ratio for targeted intervention.
10. **Longer hospital stays are not preventing early return.** Time in hospital is higher for readmitted patients, yet they still return within 30 days. The problem lies in discharge execution quality, not inpatient care intensity.

---

## Recommendations

| # | Insight | Recommendation | Expected Impact |
|---|---|---|---|
| 1 | 52.70% of early readmits discharged without medication change | **Mandate Medication Review Before Every Diabetic Discharge.** Require documented endocrinologist/pharmacist review for all diabetic inpatients; undocumented exceptions need clinical justification logged. | ~580 prevented readmissions / $8.3M saved annually (60–90 days) |
| 2 | [70-80] = highest encounter volume; 60+ = majority of readmissions | **Launch a 14-Day Post-Discharge Follow-Up for Patients Aged 60+.** Geriatric Diabetes Discharge Protocol: Day 2 phone call → Day 5 nurse visit → Day 10 telehealth → Day 14 outpatient appointment. | ~1,194 prevented readmissions / $17.2M saved annually (90–120 days) |
| 3 | Readmitted patients 3x higher prior inpatient visits; elevated labs, meds, diagnoses | **Deploy a 4-Variable Readmission Risk Score at Admission.** Score on: prior inpatient visits + diagnoses + medications + lab procedures. Top 20% trigger enhanced care pathway with dedicated case manager from Day 1. | ~460 prevented readmissions / $6.6M saved annually (90–180 days) |
| 4 | Emergency carries the largest readmitted segment in admission type data | **Introduce Specialty Discharge Checklists for Internal Medicine and Emergency.** 5-point checklist: glucose stable 24h, patient education complete, follow-up booked, medication reconciliation signed, written care plan issued. | ~270 prevented readmissions / $3.9M saved annually (30–60 days) |
| 5 | High Risk patients lead on all clinical indicators including insulin management | **Standardise Insulin Management Per ADA 2024 Guidelines.** Mandatory HbA1c on admission, standard titration protocol, diabetes nurse review within 24h, structured insulin education before discharge. | ~390 prevented readmissions / $5.6M saved annually (60–90 days) |

**Combined conservative impact: ~2,894 readmissions prevented / $41.6M saved annually within 6 months.**
> Financial basis: $14,400 avg cost per 30-day readmission (AHRQ, 2023). Impact percentages derived from peer-reviewed healthcare literature.

---

## Repository Structure

```text
SectionB_G-15_DiabeticCare/
|
|-- README.md
|
|-- data/
|   |-- raw/                         # Original dataset (never edited)
|   `-- processed/                   # Cleaned output from ETL pipeline
|
|-- notebooks/
|   |-- 01_extraction.ipynb
|   |-- 02_cleaning.ipynb
|   |-- 03_eda.ipynb
|   |-- 04_statistical_analysis.ipynb
|   `-- 05_final_load_prep.ipynb
|
|-- scripts/
|   `-- etl_pipeline.py
|
|-- tableau/
|   |-- screenshots/
|   `-- dashboard_links.md
|
|-- reports/
|   |-- README.md
|   |-- project_report_template.md
|   `-- presentation_outline.md
|
|-- docs/
|   `-- data_dictionary.md
|
|-- DVA-oriented-Resume/
`-- DVA-focused-Portfolio/
```

---

## Analytical Pipeline

The project follows a structured 7-step workflow:

1. **Define** - Healthcare sector selected; 30-day diabetic readmission identified as core problem; mentor approval obtained.
2. **Extract** - UCI Diabetic Readmission dataset (101,766 encounters, 50 columns) sourced and committed to `data/raw/`; data dictionary drafted in `docs/data_dictionary.md`.
3. **Clean and Transform** - 5-stage Python pipeline in `notebooks/02_cleaning.ipynb`: missing value treatment (`weight` dropped at 96.9% missing; `medical_specialty` imputed; `race` rows dropped at 2.2%), deduplication, binary target engineering (`readmitted_30day`), admission type label mapping, insulin flag creation. Final: 69,990 clean records.
4. **Analyze** - EDA and statistical analysis in notebooks `03` and `04`. Key findings: ~3x prior inpatient visit gap, 52.70% no-medication-change rate among readmits, age group concentration in [70-80] bracket.
5. **Visualize** - 3-view interactive Tableau dashboard published on Tableau Public: Executive Overview, Clinical Deep Dive, and Risk Segmentation — with cross-worksheet filters on Age, Admission Type, Readmission Outcome, and Risk Segment.
6. **Recommend** - 5 data-backed business recommendations with combined impact of ~$41.6M annually, each tied directly to a dashboard KPI and a specific patient cohort.
7. **Report** - Final project report and presentation deck completed and exported to PDF in `reports/`.

---

## Tech Stack

| Tool | Status | Purpose |
|---|---|---|
| Python + Jupyter Notebooks | Mandatory | ETL, cleaning, analysis, and KPI computation |
| Google Colab | Supported | Cloud notebook execution environment |
| Tableau Public | Mandatory | Dashboard design, publishing, and sharing |
| GitHub | Mandatory | Version control, collaboration, contribution audit |
| SQL | Optional | Initial data extraction only, if documented |

**Recommended Python libraries:** `pandas`, `numpy`, `matplotlib`, `seaborn`, `scipy`, `statsmodels`

---

## Evaluation Rubric

| Area | Marks | Focus |
|---|---|---|
| Problem Framing | 10 | Is the business question clear and well-scoped? |
| Data Quality and ETL | 15 | Is the cleaning pipeline thorough and documented? |
| Analysis Depth | 25 | Are statistical methods applied correctly with insight? |
| Dashboard and Visualization | 20 | Is the Tableau dashboard interactive and decision-relevant? |
| Business Recommendations | 20 | Are insights actionable and well-reasoned? |
| Storytelling and Clarity | 10 | Is the presentation professional and coherent? |
| **Total** | **100** | |

> Marks are awarded for analytical thinking and decision relevance, not chart quantity, visual decoration, or code length.

---

## Submission Checklist

**GitHub Repository**

- [x] Public repository created with the correct naming convention (`SectionName_TeamID_ProjectName`)
- [ ] All notebooks committed in `.ipynb` format
- [ ] `data/raw/` contains the original, unedited dataset
- [ ] `data/processed/` contains the cleaned pipeline output
- [ ] `tableau/screenshots/` contains dashboard screenshots
- [x] `tableau/dashboard_links.md` contains the Tableau Public URL
- [x] `docs/data_dictionary.md` is complete
- [x] `README.md` explains the project, dataset, and team
- [ ] All members have visible commits and pull requests

**Tableau Dashboard**

- [x] Published on Tableau Public and accessible via public URL
- [x] At least one interactive filter included
- [x] Dashboard directly addresses the business problem

**Project Report**

- [x] Final report exported as PDF into `reports/`
- [x] Cover page, executive summary, sector context, problem statement
- [x] Data description, cleaning methodology, KPI framework
- [x] EDA with written insights, statistical analysis results
- [x] Dashboard screenshots and explanation
- [x] 8-12 key insights in decision language
- [x] 3-5 actionable recommendations with impact estimates
- [x] Contribution matrix matches GitHub history

**Presentation Deck**

- [ ] Final presentation exported as PDF into `reports/`
- [ ] Title slide through recommendations, impact, limitations, and next steps

**Individual Assets**

- [x] DVA-oriented resume updated to include this capstone
- [x] Portfolio link or project case study added

---

## Contribution Matrix

This table must match evidence in GitHub Insights, PR history, and committed files.

| Team Member | Dataset and Sourcing | ETL and Cleaning | EDA and Analysis | Statistical Analysis | Tableau Dashboard | Report Writing | PPT and Viva |
|---|---|---|---|---|---|---|---|
| Rishav Dewan | Support | Support | Support | Support | Support | Owner | Support |
| Jatin Verma | Owner | Owner | Support | Support | Support | Support | Support |
| Archit Raj | Support | Support | Owner | Owner | Support | Support | Support |
| Shobhit | Support | Support | Support | Support | Owner | Support | Support |
| Om Kar Shukla | Support | Support | Support | Support | Support | Support | Owner |
| Rachit Singh | Support | Owner | Support | Support | Support | Owner | Support |

_Declaration: We confirm that the above contribution details are accurate and verifiable through GitHub Insights, PR history, and submitted artifacts._

**Team Lead Name:** _____________________________

**Date:** _______________

---

## Academic Integrity

All analysis, code, and recommendations in this repository must be the original work of the team listed above. Free-riding is tracked via GitHub Insights and pull request history. Any mismatch between the contribution matrix and actual commit history may result in individual grade adjustments.

---

*Newton School of Technology - Data Visualization & Analytics | Capstone 2*
