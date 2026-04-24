# Hospital Readmission Risk Analytics

> A multi-tool data analysis project exploring 30,000 patient records to identify readmission risk drivers, quantify financial impact, and recommend targeted interventions — built with SQL, Python, Excel, and Tableau.

---

## Table of contents

- [The story](#the-story)
- [Key findings](#key-findings)
- [Tech stack](#tech-stack)
- [Project structure](#project-structure)
- [Dataset](#dataset)
- [Analysis breakdown](#analysis-breakdown)
  - [SQL analysis](#sql-analysis)
  - [Python EDA](#python-eda)
  - [Excel dashboard](#excel-dashboard)
  - [Tableau dashboard](#tableau-dashboard)
- [Financial model](#financial-model)
- [Challenges](#challenges)
- [Recommendations](#recommendations)
- [Conclusion](#conclusion)
- [How to run](#how-to-run)

---

## The story

Every year, hospitals lose millions — not just in money, but in trust — when patients come back through the same doors within 30 days of being discharged. These readmissions are expensive, often preventable, and a signal that something in the care pathway broke down.

This project started with a single question: **can we predict which patients will come back, before they leave?**

The dataset had 30,000 patient records, 12 variables, and no obvious answers upfront. The first instinct — shared by most people entering healthcare analytics — was that the usual suspects would be guilty: older patients, diabetics, people with high blood pressure, patients who stayed longer. That instinct turned out to be wrong in almost every case.

The analysis unfolded in four stages across four tools, each one peeling back a layer.

**Stage 1 — Baseline (SQL):** The overall readmission rate was 12.25%. About 1 in 8 patients returned within 30 days. Older patients had marginally higher rates, but the gap was so small it was practically noise. Diabetics and non-diabetics had nearly identical hospital stays. Medication count showed no consistent relationship with length of stay. The data was telling us something important early on: *individual variables don't explain much on their own.*

**Stage 2 — Pattern finding (Python):** A correlation heatmap confirmed that no single feature was strongly correlated with readmission. This isn't a failure — it's a finding. It means readmission is a complex, compounding problem. Digging into segmentation revealed that the combination of diabetes, age group 50–70, and medication count ≥ 5 produced the worst outcomes (success rate: 86.1%). Not any one of those three — all three together.

**Stage 3 — Structural gap (Tableau):** When discharge destination was layered in, a critical disconnect emerged. Patients sent to Rehab had a 17.5% readmission rate. Nursing Facility: 16.8%. Home: 10.0%. Superficially, this looks like those settings are failing patients. But when risk scores were mapped against discharge decisions, a different story appeared: **70% of the top 10% highest-risk patients were being sent home.** The higher readmission rates at Rehab and Nursing Facility weren't because those settings were worse — they were receiving more complex patients in the first place. The real problem was that the discharge decision wasn't being made with risk data in hand.

**Stage 4 — Business case (SQL + Python):** A rule-based intervention model targeting diabetic patients with high medication counts produced a 22% ROI. Switching to a risk-score-based model — targeting only the top 20% of highest-risk patients with tiered intervention costs — pushed ROI to 144%, generating £2.1M in net benefit. An A/B test simulation showed that a structured follow-up programme would reduce readmissions from 11.45% to 10.95%, with the strongest effect among Rehab patients (~1.5% reduction, ~£1.5M in savings).

The conclusion of the analysis wasn't just a list of recommendations. It was a reframe: **the problem isn't that hospitals are treating patients badly — it's that the system isn't using available data to make smarter decisions at the point of discharge.**

---

## Key findings

| Finding | Detail |
|---|---|
| Overall readmission rate | 12.25% (~1 in 8 patients) |
| Single-variable predictors | Weak across the board — age, BMI, diabetes, hypertension, medication count alone all show minimal impact |
| Top risk driver | Compound risk: diabetes + high medication count + age 50–70 together, not individually |
| Critical discharge gap | 70% of the top 10% highest-risk patients are discharged home, despite significantly elevated readmission risk |
| Readmission by destination | Rehab: 17.5% · Nursing Facility: 16.8% · Home: 10.0% |
| Worst patient segment | Diabetic, age 50–70, medication count ≥ 5 — success rate of just 86.1% |
| A/B test outcome | Follow-up intervention reduced readmissions from 11.45% → 10.95% (4.4% relative improvement) |
| Total readmission cost | ~£55M across all 30,000 patients (at £15,000 per readmission) |
| Optimal ROI strategy | Top 20% by risk score + tiered interventions → 144% ROI, £2.1M net benefit |

---

## Tech stack

| Tool | Purpose |
|---|---|
| MySQL Workbench | SQL queries, window functions, risk scoring, A/B simulation, funnel analysis |
| Python (pandas, seaborn, matplotlib) | EDA, correlation analysis, segmentation, cost-benefit and ROI modelling |
| Microsoft Excel | Pivot tables, interactive dashboard with slicers |
| Tableau | Multi-sheet analytical dashboard with filters, heatmaps, and cohort views |

---

## Project structure

```
hospital-readmission-risk-analytics/
│
├── data/
│   └── hosp.csv                    # Raw dataset (30,000 patient records)
│
├── sql/
│   └── hosp_analysis.sql           # All SQL queries with inline commentary
│
├── python/
│   └── hospital_eda.ipynb          # Jupyter notebook — full EDA and modelling
│
├── excel/
│   └── hospital_readmissions.xlsx  # Cleaned data, pivot tables, dashboard
│
├── tableau/
│   └── hospital_dashboard.twbx     # Packaged Tableau workbook (2 dashboards)
│
└── README.md
```

---

## Dataset

**File:** `hosp.csv`  
**Rows:** 30,000 patient records  
**Columns:** 12 features, no missing values

| Column | Type | Description |
|---|---|---|
| `age` | int | Patient age (18–90) |
| `gender` | string | Male / Female / Other |
| `cholesterol` | int | Cholesterol level (150–300) |
| `bmi` | float | Body mass index (18–40) |
| `diabetes` | binary | 1 = diabetic, 0 = not |
| `hypertension` | binary | 1 = hypertensive, 0 = not |
| `medication_count` | int | Number of medications (0–10) |
| `length_of_stay` | int | Days admitted (1–10) |
| `discharge_destination` | string | Home / Rehab / Nursing_Facility |
| `readmitted_30_days` | binary | 1 = readmitted within 30 days |
| `systolic_bp` | int | Systolic blood pressure |
| `diastolic_bp` | int | Diastolic blood pressure |

---

## Analysis breakdown

### SQL analysis

15+ queries covering four progressive layers:

**Descriptive**
- Total patient count, average age, overall readmission rate
- Readmission rate by age group, discharge destination, and risk group

**Diagnostic**
- Diabetes vs. length of stay — no meaningful difference found
- Medication count vs. hospital stay — stable across all medication levels
- Age × medication level segmentation heatmap

**Risk modelling**
- Custom risk score formula: `(medication_count × 0.4) + (diabetes × 2) + (length_of_stay × 0.3)`
- NTILE(5) window function to segment all patients into five risk groups
- Validation: highest-risk group had the highest readmission rate (13.5%), confirming the model
- Top 10% highest-risk discharge pattern analysis

**Experimental**
- A/B test simulation: control vs. follow-up intervention group across all discharge destinations
- Funnel analysis: Total → High Complexity → Discharged to Rehab/Nursing → Readmitted
- Financial impact: £55M total cost, £12M attributable to top 20% high-risk patients

---

### Python EDA

**Libraries:** pandas, matplotlib, seaborn

**Key analyses:**
- Age and BMI distribution — both uniform, ruling out demographic bias in the dataset
- Correlation heatmap — near-zero correlations across all features, establishing that single-variable models will underperform
- Length of stay by condition group (violin plots) — no significant differences between diabetes, hypertension, both, or neither
- Readmission risk heatmap by age × medication level — compound segmentation reveals pockets of elevated risk invisible in individual views
- Patient risk vs. cost segmentation scatter — higher medication count patients show higher readmission risk but not longer stays, pointing to post-discharge as the real gap
- ROI optimisation curve — models diminishing returns as intervention broadens; optimal ROI at top 10%
- Cost-benefit model with tiered intervention pricing
- Event sequence mapping — traces common patient journeys from admission to outcome, identifying failure paths

**Worst-performing segment identified:**
- Diabetes = 1, age group = 50–70, medication count ≥ 5
- Success rate: **86.1%** — the lowest across all segments in the dataset

---

### Excel dashboard

Four-sheet workbook:

| Sheet | Contents |
|---|---|
| `cleaned_hospital` | Cleaned and formatted raw data |
| `Pivot table` | Aggregations by key dimensions |
| `dashboard` | Interactive dashboard with slicers |
| `Sheet4` | Supporting calculations |

Dashboard visuals:
- High risk distribution (pie chart — 54% high risk, 46% normal)
- Frequent patients vs. length of stay (donut — frequent patients drive disproportionate resource use)
- BMI trend across age groups (line chart — BMI stable at ~29–30, ruling it out as a driver)
- Age vs. readmission rate (bar chart — consistent rates across all age groups)
- Slicers: age, gender, frequent patient category

---

### Tableau dashboard

Two dashboards across 7 sheets:

**Dashboard 1**
- Readmission by discharge destination (bar — surfaces the Home / Nursing / Rehab gap)
- Readmission risk heatmap (Stay Group × Med Group — patients with long stays and high medication load show 12.9%+ readmission)
- Cohort analysis — time/group clustering (bubble chart — high medication patients cluster tightly, showing systematic not random behaviour)
- Cost and risk efficiency map (treemap — older diabetic patients discharged home flagged as highest-risk, lowest-care group)

**Dashboard 2**
- Cost vs. risk efficiency (scatter with length-of-stay slider — shows higher spending is not translating to reduced readmissions in Nursing Facility patients)
- Readmission risk vs. patient risk score (line chart — Rehab and Nursing patients consistently above Home across all risk levels)
- Decision-making visualisation (grouped bars — Home delivers lowest readmission AND lowest cost; Nursing/Rehab deliver higher cost AND higher readmission)
- Wrong discharge by age and disease (supporting analysis surfacing the highest-risk, worst-served patient groups)

---


---

## Challenges

Every analysis hits friction. Here is what made this one difficult, and how each problem was worked through.

**1. No single variable explained anything**

The most disorienting moment in this project was opening the correlation heatmap and finding near-zero values everywhere. In most healthcare analyses, you expect at least age or comorbidity burden to show a clear signal. Here, nothing did individually. The temptation is to conclude the data is bad. The correct conclusion — which took time to arrive at — is that the problem is inherently multivariate. That reframe led to building compound segmentation and the custom risk score, both of which produced results the single-variable queries never could.

**2. The discharge destination data was misleading at first glance**

When Rehab and Nursing Facility readmission rates came back at 17–18% vs. 10% for Home, the initial read was that those settings were failing patients. This is a classic confounding problem. The rates were higher not because Rehab was worse, but because it was receiving more complex patients. Untangling this required cross-referencing discharge destination with risk group — only then did the real picture emerge: high-risk patients were being sent home at the same rate as low-risk patients, which was the actual problem. The lesson here is that rates without context are often misleading, and stratification is the tool that adds the context.

**3. Simulating the A/B test without a live experiment**

A real A/B test requires randomised assignment over time and a follow-up period. Since this dataset is a static snapshot, both had to be simulated. The experiment group was assigned using `MOD(ROW_NUMBER() OVER(), 2)` and treatment effect was modelled probabilistically with `RAND() < 0.15`. This is not a substitute for a real experiment — the results should be treated as directional estimates, not causal proof. The output was clearly labelled as simulated throughout, and the purpose was to demonstrate what the analysis pipeline would look like and what approximate effect sizes to expect when a real trial is run.

**4. Balancing depth across four tools without duplicating work**

Using SQL, Python, Excel, and Tableau simultaneously creates a real risk of repetition — the same chart appearing in multiple places without adding new information. The discipline here was to assign each tool a distinct analytical role: SQL for the logic, modelling, and financial quantification; Python for distributional insight and economic optimisation; Excel for accessible business-facing reporting; and Tableau for the interactive exploratory layer. Each tool had to earn its place by contributing something the others did not.

**5. Communicating "absence of effect" as a finding**

Telling a stakeholder that age doesn't matter, BMI doesn't matter, and length of stay doesn't matter — when those are the variables most people assume are driving readmission — is harder than it sounds. The challenge was framing these null results not as dead ends but as data that clears the ground for the real drivers. That reframe was central to making the final recommendations feel grounded and specific rather than arbitrary. Absence of evidence, presented clearly, is evidence — and it deserves as much attention as a positive finding.

---

## Recommendations

**1. Implement a risk score at the point of discharge**

Before any patient leaves the ward, calculate their composite risk score using the formula developed in this analysis. Flag anyone in the top 20% for an automatic escalation review. This does not require machine learning — the SQL-based scoring model is sufficient as a first implementation and can run against any patient management system that supports basic queries.

**2. Close the discharge destination gap**

Seventy percent of the highest-risk patients are currently going home. This is not necessarily wrong in every case, but it should be a deliberate clinical decision, not a default. A structured triage protocol at discharge — informed by risk score — should ensure that high-risk patients are reviewed for Rehab or Nursing Facility placement before being discharged without support.

**3. Roll out the follow-up intervention for Rehab patients first**

The A/B test simulation showed the strongest treatment effect among patients discharged to Rehab (~1.5 percentage point reduction in readmissions). This translates to roughly £1.5M in potential savings for that group alone. A targeted pilot here — before broader rollout — would validate the model with real outcomes and provide the evidence base for scaling to other discharge pathways.

**4. Stop using single variables to assess risk**

Age alone, medication count alone, and diabetes status alone are all weak predictors. Any risk stratification process built on individual thresholds (e.g., "flag all patients over 70") will misclassify large portions of the population. The compound risk score — combining medication count, diabetes status, and length of stay — consistently outperformed single-variable rules across every analysis layer in this project.

**5. Prioritise the diabetic 50–70 cohort for immediate review**

This segment appeared at the bottom of the success rate ranking in every segmentation approach attempted. With a success rate of 86.1% and consistently high readmission risk across both SQL and Python analyses, this group warrants a dedicated care pathway — not as part of a general intervention, but as a named clinical priority with its own monitoring and follow-up protocol.

---

## Conclusion

This project set out to find which patients come back to hospital within 30 days — and why. What it found was more instructive than a simple list of risk factors.

The data consistently showed that readmission is not a single-variable problem. Age, BMI, diabetes, hypertension, medication count, length of stay — each one, examined alone, explained almost nothing. This is counterintuitive, and it took systematic elimination across four tools to establish it clearly. The implication is significant: any intervention built on a single-variable rule will miss most of the patients it is trying to catch.

What does predict readmission is the compound interaction between patient complexity, treatment burden, and — most critically — what happens after discharge. The discharge destination analysis revealed that the hospital's current process is not stratifying risk at the point of leaving. High-risk patients are walking out through the same doors as low-risk ones, with the same follow-up (or lack of it). That is the structural gap this project identified, and it is the most actionable finding in the entire analysis.

The financial model made the case concrete. Readmissions are costing approximately £55M across this patient population. A targeted intervention — risk-scored, tiered by cost, focused on the right 20% of patients — could generate £2.1M in net savings at a 144% ROI. The ROI optimisation curve showed clearly that smarter targeting, not more spending, is what drives that number. Broadening the intervention beyond the top 20% costs more and saves less — a finding that runs counter to the instinct to treat as many people as possible.

Perhaps the most important lesson from this project is one that applies well beyond healthcare: **the assumptions we bring to data are often the first things the data disproves**. The variables expected to matter — age, individual conditions, length of stay — did not. The structural problem no one was explicitly looking for — that discharge decisions are being made without risk data — turned out to be the central finding. Getting there required being willing to follow null results as seriously as positive ones, and to let the data revise the question rather than just answer it.

If implemented, the recommendations in this project would not merely reduce readmissions. They would change how discharge decisions are made — from a clinical judgment call made without data, to a structured, risk-informed process that uses what the hospital already knows about each patient to determine what they need next. That is the difference between reacting to readmissions after the fact and preventing them before the patient reaches the car park.

---
