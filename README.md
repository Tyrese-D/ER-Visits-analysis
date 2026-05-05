# Hospital Emergency Room — Data Cleaning, ETL & Dashboard

Dashboard link: https://public.tableau.com/app/profile/tyrese.dieudonne/viz/HospitalEmergencyroomvisitDashboard_17777488311500/EmergencyRoomVisitsDS

## Project Overview
This project focuses on transforming raw Hospital Emergency Room data into a
clean, analysis-ready dataset and building an interactive dashboard that gives
hospital leadership a full view of patient volume, wait times, and satisfaction
scores across demographics, departments, and time periods. The dataset covers
9,216 ER visit records spanning patient demographics, wait times, satisfaction
scores, and department referrals.

## Technical Stack
- **Data Management:** Python (pandas)
- **Validation & Profiling:** `.isnull()`, `.duplicated()`, `.describe()`, `.value_counts()`
- **Date Handling:** `pd.to_datetime()` for type conversion and time-based feature extraction
- **Visualization:** Tableau — used to build the Emergency Room Visits Dashboard
- **Data Sources:** Flat CSV containing Hospital ER visit records across 11 columns

## The Data Analysis Process
I followed a 3-stage ETL framework to move from raw data to a finished dashboard:

1. **Extract:** Loaded the raw CSV into a pandas DataFrame as a staging
   environment, confirmed the shape as 9,216 rows and 11 columns, and
   immediately flagged that the `date` column was loaded as a plain string
   instead of a datetime object, that `patient_sat_score` had a 72.7% null
   rate, and that `department_referral` was missing for 58.6% of records
2. **Transform:** Applied all cleaning operations across six categories —
   null handling, duplicate validation, gender encoding standardization,
   date type conversion, satisfaction score missing value strategy, and
   department referral missing value strategy
3. **Dashboard Construction:** Built the Emergency Room Visits Dashboard in
   Tableau across three sections — Demographic, Wait Time, and Satisfaction
   Score — covering KPIs, trend lines, age group breakdowns, department
   referral distributions, race breakdowns, daily heatmaps, and admission
   status comparisons

## Data Cleaning Steps

### Null & Missing Values
- **Null Check:** Ran `.isnull().sum()` across all 11 columns and found
  nulls concentrated in two columns — `patient_sat_score` and
  `department_referral`
- **Patient Satisfaction Score Nulls:**
  - Found **6,699 null values** out of 9,216 records — a **72.7% missing rate**
  - This is not a data error — satisfaction scores are only collected from
    patients who choose to complete a survey, meaning most patients never
    submit one
  - Did not impute these nulls with a mean or median — doing so would
    fabricate satisfaction data for nearly three-quarters of all patients
    and distort every satisfaction metric downstream
  - Treated nulls as `"Not Surveyed"` in categorical analyses and excluded
    them from any average satisfaction score calculation so that reported
    averages only reflect patients who actually responded
  - This is why the dashboard reports an average satisfaction of **4.99/10**
    calculated from the 2,517 surveyed patients only — not all 9,216
- **Department Referral Nulls:**
  - Found **5,400 null values** out of 9,216 records — a **58.6% missing rate**
  - A null referral means the patient was not referred to any department —
    a valid and meaningful ER outcome, not a missing data problem
  - Recoded all null values to `"None"` so the no-referral outcome could
    be counted, filtered, and trended in Tableau the same way any named
    department would be
  - This is why `"None"` appears as its own bar in the department referral
    wait time chart on the dashboard alongside named departments

### Duplicate Records
- **Duplicate Check:** Ran `df.duplicated().sum()` across all 11 columns
- Returned **zero exact duplicate rows** — every record was unique
- Verified against `patient_id` — confirmed all 9,216 patient IDs were
  unique with no patient appearing more than once
- `patient_id` follows a consistent `XXX-XX-XXXX` format with no
  formatting inconsistencies — validated as a reliable primary key for
  joins and future deduplication checks

### Gender Encoding Standardization
- **Discovery:** Ran `.value_counts()` on `patient_gender` and found three
  values — `M` (4,705), `F` (4,487), and `NC` (24)
- **Issue:** `NC` is an undocumented encoding that could mean "No Choice",
  "Not Collected", or "Non-Conforming" depending on the source system —
  different analysts would interpret it differently
- **Resolution:**
  - Documented `NC` as a third valid category rather than dropping those
    24 records or forcing them into M or F
  - Standardized the column to full labels — `Male`, `Female`, and
    `Not Collected` — rather than single-letter codes to prevent
    misinterpretation across tools and analysts
  - Enforced the three-value controlled vocabulary at the schema level

### Date Type Conversion & Feature Extraction
- **Type Conversion:** The `date` column was loaded as a plain string
  containing both date and timestamp — for example `2020-03-20 08:47:01`
- Converted to a proper **datetime object** using `pd.to_datetime()` so
  that date arithmetic, filtering, and time-based aggregations behave
  consistently across every tool and query
- Extracted additional time-based features from the converted column:
  - **Hour of visit** — used to power the daily average wait time heatmap
    on the dashboard showing 12 AM through 7 AM by day of week
  - **Day of week** — used to identify peak and low-volume days across
    Sunday through Saturday in the heatmap
  - **Month** — used to power the trend sparklines in the KPI cards at
    the top of the dashboard
  - **Year** — used to enable year-over-year comparisons in trend views

### Patient Age Grouping
- **Range Check:** `patient_age` ranged from **1 to 79** with a mean of
  approximately 39.9 — no invalid values were found
- Created three age group buckets to match the dashboard breakdowns:
  - **0-18yrs** — pediatric patients — 23% of total volume
  - **19-65yrs** — adult patients — 60% of total volume
  - **66yrs+** — elderly patients — 18% of total volume
- These buckets feed the age group breakdowns visible in all three
  dashboard sections — patient count, average wait time, and satisfaction
  score by age group

### Wait Time Validation
- **Range Check:** `patient_waittime` ranged from **10 to 60 minutes**
  with a mean of approximately **35.26 minutes** — matching exactly the
  KPI displayed on the dashboard
- No negative wait times and no impossibly large values were found
- The distribution was smooth and consistent with realistic ER patterns

### Data Type Enforcement
- **Date:** Enforced as **DATETIME** — not VARCHAR
- **Age and Wait Time:** Enforced as **INTEGER**
- **Satisfaction Score:** Enforced as **FLOAT** — valid scores include
  decimal values between 0.0 and 10.0
- **Admin Flag:** Enforced as **BOOLEAN** — only True or False
- **Gender, Race, Department Referral:** Enforced as standardized string
  categories with controlled vocabularies

## Dashboard Construction

### Overview
- Built the **Emergency Room Visits Dashboard** in Tableau organized across
  three vertical sections — Demographic, Wait Time, and Satisfaction Score
- Each section contains a KPI card at the top with a trend sparkline
  followed by breakdowns by age group, admission status, department
  referral, and race

### Demographic Section
- **KPI Card:** Total patient count of **9,216** with a trend sparkline
  showing monthly volume — peaks at 530 and a low of 431 visible in the
  miniature line chart
- **Patients by Age Group:** Horizontal bar chart showing 19-65yrs at 60%,
  0-18yrs at 23%, and 66yrs+ at 18% of total ER volume
- **Patients by Department Referral:** Horizontal bar chart ranking all
  seven referral departments — General Practice leading at 1,840 referrals,
  followed by Orthopedics at 995, Physiotherapy at 276, Cardiology at 248,
  Neurology at 193, Gastroenterology at 178, and Renal at 86
- **Patients by Race:** Horizontal bar chart showing White at 2,571,
  African American at 1,951, Two or More Races at 1,557, Asian at 1,060,
  Declined to Identify at 1,030, Pacific Islander at 549, and Native
  American/Alaska Native at 498

### Wait Time Section
- **KPI Card:** Average patient wait time of **35.26 minutes** with a
  trend sparkline showing fluctuations — peaking at 37 minutes and a
  low of 34 minutes across the reporting period
- **Wait Time by Admission Status:** Bar chart comparing Non-Admitted
  patients averaging 35.6 minutes versus Admitted patients averaging
  35.0 minutes — a small but consistent gap
- **Wait Time by Department Referral:** Horizontal bar chart ranking all
  departments by average wait — Neurology highest at 36.8 mins, followed
  by Physiotherapy at 36.6, Gastroenterology at 35.8, Cardiology at 35.4,
  None at 35.3, Orthopedics at 35.0, General Practice at 34.9, and Renal
  lowest at 34.7 minutes
- **Daily Average Wait Time Heatmap:** Hour-by-day matrix covering 12 AM
  through 7 AM across Sunday through Saturday — cells highlighted in orange
  and red indicate peak wait time periods, allowing hospital operations to
  identify exactly which hours and days require additional staffing

### Satisfaction Score Section
- **KPI Card:** Average patient satisfaction of **4.99/10** with a trend
  sparkline — peaking at 5/3 and dipping to 4.6 across the reporting period
- **Satisfaction by Age Group:** Bar chart showing 19-65yrs scoring highest
  at 5.1, 0-18yrs at 5.0, and 66yrs+ lowest at 4.7 — elderly patients
  reported the lowest satisfaction of any age group
- **Satisfaction by Admission Status:** Bar chart showing Admitted patients
  scoring 5.1 versus Non-Admitted at 4.9 — admitted patients reported a
  slightly higher experience
- **Satisfaction by Department Referral:** Horizontal bar chart ranking
  departments — Gastroenterology highest at 5.8, Neurology at 5.3,
  Cardiology at 5.1, General Practice at 5.1, Physiotherapy at 5.0,
  Orthopedics at 4.9, and Renal lowest at 4.6

## Business Impact

### Handling 6,699 Missing Satisfaction Scores
- **Risk Prevented:** Imputing a mean score for 72.7% of records would have
  artificially compressed the satisfaction distribution — making every
  reporting period appear average and hiding genuine spikes of poor or
  excellent patient experience
- **Operational Impact:** Hospital administrators tracking satisfaction as a
  KPI would have been unable to identify which departments or demographics
  were driving dissatisfaction — the fabricated scores would have smoothed
  out every real signal in the data
- **Dashboard Impact:** The 4.99/10 average displayed on the dashboard
  reflects only the 2,517 patients who actually submitted a score — making
  it a trustworthy signal that leadership can act on rather than a
  statistically diluted number inflated by fabricated responses

### Recoding 5,400 Missing Department Referrals
- **Risk Prevented:** Leaving nulls as blank would have caused them to be
  silently dropped from every GROUP BY aggregation — making it appear that
  only 3,816 patients had a documented outcome when all 9,216 did
- **Operational Impact:** Department referral data drives resource allocation
  decisions — how many patients flow into General Practice versus Cardiology
  versus Orthopedics. A dashboard excluding 58.6% of all visits from that
  breakdown would produce completely misleading referral volumes, causing
  departments to be under- or over-resourced based on phantom data
- **Dashboard Impact:** The `"None"` bar visible in the department referral
  wait time chart exists because of this recoding step — without it, the
  no-referral outcome would have been invisible in Tableau, and the wait
  time comparison across outcomes would have been incomplete

### Standardizing Gender Encoding
- **Risk Prevented:** The undocumented `NC` encoding would have been
  interpreted differently by every analyst — some dropping those records,
  some treating it as an error — producing inconsistent gender breakdowns
  across different reports built on the same data
- **Operational Impact:** Standardizing to `Male`, `Female`, and
  `Not Collected` means every dashboard and downstream model interprets
  those 24 records consistently — no records are silently lost and no
  encoding is misread

### Converting Date Strings to Datetime Objects
- **Risk Prevented:** String-stored timestamps cannot be reliably filtered
  by date range or grouped by month — two analysts writing the same query
  with slightly different string formatting would get different results
  from identical underlying data
- **Dashboard Impact:** The trend sparklines in all three KPI cards, the
  daily wait time heatmap broken down by hour and day of week, and all
  monthly volume aggregations visible on the dashboard depend entirely on
  correctly typed datetime values — none of those visualizations would have
  been buildable from string-formatted timestamps

### Creating Age Group Buckets
- **Operational Impact:** Raw age values ranging from 1 to 79 are not
  directly usable in a dashboard dimension — grouping them into 0-18yrs,
  19-65yrs, and 66yrs+ enabled the three age group breakdowns visible
  across all sections of the dashboard
- **Insight Unlocked:** The grouping revealed that elderly patients (66yrs+)
  report the lowest satisfaction at 4.7/10 compared to 5.1 for adults —
  a finding that would not have been visible without the bucketing step
  and that gives hospital leadership a clear demographic priority for
  experience improvement initiatives

### Enforcing Schema Constraints at Load
- **Risk Prevented:** Without constraints, future data loads could silently
  introduce out-of-range satisfaction scores, impossible ages, undocumented
  gender codes, or negative wait times — corrupting the warehouse without
  producing any visible error
- **Operational Impact:** Any future record violating a constraint is
  rejected and logged at ingestion, creating an auditable trail of data
  quality incidents rather than allowing bad data to accumulate undetected
  and corrupt dashboard metrics months after the initial cleanup

## Key Insights
- **Volume:** The ER handled 9,216 total patient visits with monthly volume
  ranging from a low of 431 to a peak of 530
- **Wait Times:** Average wait time across all patients was 35.26 minutes —
  Neurology referrals waited the longest at 36.8 minutes and Renal the
  shortest at 34.7 minutes
- **Satisfaction:** Average satisfaction among surveyed patients was 4.99/10
  — Gastroenterology scored highest at 5.8 and Renal lowest at 4.6
- **Demographics:** Adults 19-65 made up 60% of all ER visits — White
  patients were the largest racial group at 2,571 visits followed by
  African American at 1,951
- **Peak Hours:** The daily heatmap identified specific hour-and-day
  combinations with consistently elevated wait times — giving operations
  leadership a data-driven basis for shift scheduling decisions



