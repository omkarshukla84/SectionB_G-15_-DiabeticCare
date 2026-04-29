# Data Dictionary Template

Use this file to document every important field in your dataset. A strong data dictionary makes your cleaning decisions, KPI logic, and dashboard filters much easier to review.

## How To Use This File

1. Add one row for each column used in analysis or dashboarding.
2. Explain what the field means in plain language.
3. Mention any cleaning or standardization applied.
4. Flag nullable columns, derived fields, and known quality issues.

## Dataset Summary

| Item | Details |
|---|---|
| Dataset name | Diabetic Patient Readmission |
| Source | UCI Machine Learning Repository / Health Facts |
| Raw file name | diabetic_data.csv |
| Last updated | April 2026 |
| Granularity | One row per patient encounter (after dropping readmission duplicates) |

## Column Definitions

| Column Name | Data Type | Description | Example Value | Used In | Cleaning Notes |
|---|---|---|---|---|---|
| `race` | string | Patient race | `Caucasian` | EDA, Tableau | Imputed missing values with mode. |
| `gender` | string | Patient gender | `Female` | EDA, Tableau | |
| `age` | string | Patient age bracket | `[80-90)` | EDA, Tableau | Converted to numeric midpoint in a separate step. |
| `admission_type_id` | string | Admission type | `Urgent` | EDA, Tableau | Mapped to human-readable strings. |
| `discharge_disposition_id` | string | Discharge destination | `Discharged to Home` | EDA, Tableau | Mapped to strings. Deceased/hospice removed. |
| `admission_source_id` | string | Source of admission | `Emergency Room` | EDA, Tableau | Mapped to human-readable strings. |
| `time_in_hospital` | int | Days in hospital | `13` | EDA, Tableau | |
| `medical_specialty` | string | Admitting physician specialty | `InternalMedicine` | EDA | Missing filled with 'Unknown'. |
| `num_lab_procedures` | int | Number of lab tests | `68` | EDA, Tableau | |
| `num_medications` | int | Number of meds administered | `28` | EDA, Tableau | |
| `readmitted_30day` | int | Target variable (30-day readmission) | `0` or `1` | KPI, Tableau | Derived from `readmitted` (1 if <30, else 0). |

## Derived Columns

| Derived Column | Logic | Business Meaning |
|---|---|---|
| `readmission_status` | String mapping of `readmitted_30day` | Explicit label (Readmitted vs Not Readmitted) for dashboarding. |
| `age_risk_group` | Binned groupings of patient age | Useful to categorize patient age segments for readmission risk. |
| `clinical_complexity` | Formula based on medications, diagnoses, and procedures | Highlights overall health complication of the encounter. |
| `complexity_level` | Binned `clinical_complexity` | Categorical severity flag (Low, Medium, High) for clinical complexity. |
| `prior_visits_total` | Sum of outpatient, inpatient, and emergency visits | Indicator of healthcare utilization history. |
| `repeat_visitor_label` | Groupings based on `prior_visits_total` | Indicates if patient is a chronic user (e.g. First/Low Contact, Frequent). |
| `insulin_status` | Regimen trend (Up, Down, Steady, No) | Determines if patient's insulin regimen changed during visit. |
| `a1c_status` | Status of A1C test | Crucial metric for diabetes monitoring and management. |
| `med_changed` | Medication change indicator | Highlights changes in overall treatment plans. |
| `stay_category` | Binning of `time_in_hospital` | Simplified visualization of stay length (Short, Long, Extended). |
| `risk_flag_label` | High risk flag assignment | Direct identification of patients with high risk of readmission. |

## Data Quality Notes

- **Missing Data Handling:** `weight` was dropped entirely due to 96.9% missing data. Missing values for `max_glu_serum` and `a1cresult` were mapped to 'None'. `medical_specialty` and `payer_code` nulls were mapped to 'Unknown'. `race` was imputed with the mode. `diag_2` and `diag_3` missing values were replaced with 0.
- **Data Leakage Prevention:** Removed duplicate patient encounters, keeping only the first encounter per patient. Also removed deceased or hospice patients (discharge IDs 11, 13, 14, 19, 20, 21) since they cannot be readmitted.
- **Zero Variance:** Dropped `examide` and `citoglipton` due to zero variance across all records.
- **Standardization:** All final column names were converted to lowercase snake_case for consistency.
- **Target Variable:** The original `readmitted` variable was converted into a binary `readmitted_30day` flag (rate = 8.98%) and its text equivalent `readmission_status` to focus on the 30-day KPI.
