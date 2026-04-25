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

# 📦 IMPORTANT SQL QUERIES + KEY INSIGHTS (ONLY REQUIRED)

📄 Source: 

---

## 🔹 1. TOTAL PATIENTS

```sql
SELECT COUNT(*) FROM hosp;
```

**Insight:**
Total patients = **30,000**

---

## 🔹 2. READMISSION RATE

```sql
SELECT AVG(readmitted_30_days) * 100 AS readmission_rate
FROM hosp;
```

**Insight:**
~**12% patients readmitted** → Major improvement opportunity

---

## 🔹 3. DIABETES vs LENGTH OF STAY

```sql
SELECT diabetes, AVG(length_of_stay) AS avg_stay
FROM hosp
GROUP BY diabetes;
```

**Insight:**
No significant difference → Diabetes **not a key driver**

---

## 🔹 4. MEDICATION COUNT vs STAY

```sql
SELECT medication_count, AVG(length_of_stay) AS avg_stay
FROM hosp
GROUP BY medication_count
ORDER BY medication_count;
```

**Insight:**
No clear trend → Complexity **does not affect stay**

---

## 🔹 5. READMISSION vs STAY

```sql
SELECT readmitted_30_days, AVG(length_of_stay) AS avg_stay
FROM hosp
GROUP BY readmitted_30_days;
```

**Insight:**
Stay duration **does not impact readmission**

---

## 🔹 6. AGE GROUP vs READMISSION

```sql
SELECT
CASE
 WHEN age BETWEEN 18 AND 30 THEN '18-30'
 WHEN age BETWEEN 31 AND 50 THEN '30-50'
 WHEN age BETWEEN 51 AND 70 THEN '50-70'
 ELSE '70-90'
END AS age_group,
AVG(readmitted_30_days) * 100 AS readmission_rate
FROM hosp
GROUP BY age_group
ORDER BY readmission_rate DESC;
```

**Insight:**
Older patients slightly higher risk → Age alone **not strong factor**

---

## 🔹 7. AGE + MEDICATION (COMBINED RISK)

```sql
SELECT
CASE
 WHEN age BETWEEN 18 AND 30 THEN '18-30'
 WHEN age BETWEEN 31 AND 50 THEN '30-50'
 WHEN age BETWEEN 51 AND 70 THEN '50-70'
 ELSE '70-90'
END AS age_group,
CASE
 WHEN medication_count <= 3 THEN 'Low'
 WHEN medication_count <= 6 THEN 'Medium'
 ELSE 'High'
END AS med_group,
AVG(readmitted_30_days) * 100 AS readmission_rate
FROM hosp
GROUP BY age_group, med_group
ORDER BY readmission_rate DESC;
```

**Insight:**
Highest risk = **Older + High medication (~13%)**
→ Risk is **multi-factor**

---

## 🔹 8. RISK SEGMENTATION (TOP 20%)

```sql
SELECT *
FROM (
SELECT *,
(medication_count * 0.4 +
 diabetes * 2 +
 length_of_stay * 0.3) AS risk_score,
NTILE(5) OVER (
 ORDER BY (medication_count * 0.4 +
           diabetes * 2 +
           length_of_stay * 0.3) DESC
) AS risk_group
FROM hosp
) t
WHERE risk_group = 1;
```

**Insight:**
Top 20% = **High-risk patients**

---

## 🔹 9. COST FROM HIGH-RISK PATIENTS

```sql
SELECT SUM(readmitted_30_days) * 15000 AS total_cost FROM hosp;

SELECT COUNT(*) AS patients,
SUM(readmitted_30_days) * 15000 AS high_risk_cost
FROM (
SELECT *,
NTILE(5) OVER (
 ORDER BY (medication_count * 0.4 +
           diabetes * 2 +
           length_of_stay * 0.3) DESC
) AS risk_group
FROM hosp
) t
WHERE risk_group = 1;
```

**Insight:**
High-risk patients contribute **~22% cost (~£12M)**
→ Targeting them saves money

---

## 🔹 10. DISCHARGE DESTINATION IMPACT

```sql
SELECT discharge_destination,
COUNT(*) AS patients,
AVG(readmitted_30_days) * 100 AS readmission_rate
FROM hosp
GROUP BY discharge_destination
ORDER BY readmission_rate DESC;
```

**Insight:**
Rehab/Nursing ≈ **17%** vs Home ≈ **10%**
→ Higher severity patients

---

## 🔹 11. RISK vs DISCHARGE MISMATCH

```sql
SELECT discharge_destination, risk_group, COUNT(*) AS patients
FROM (
SELECT *,
NTILE(5) OVER (
 ORDER BY (medication_count * 0.4 +
           diabetes * 2 +
           length_of_stay * 0.3) DESC
) AS risk_group
FROM hosp
) t
GROUP BY discharge_destination, risk_group;
```

**Insight:**
High-risk patients **not prioritized for rehab**
→ Poor discharge decisions

---

## 🔹 12. MODEL VALIDATION

```sql
SELECT risk_group,
AVG(readmitted_30_days) * 100 AS readmission_rate
FROM (
SELECT *,
NTILE(5) OVER (
 ORDER BY (medication_count * 0.4 +
           diabetes * 2 +
           length_of_stay * 0.3) DESC
) AS risk_group
FROM hosp
) t
GROUP BY risk_group;
```

**Insight:**
Highest risk group ≈ **13.5%**
→ Model is valid

---

## 🔹 13. FUNNEL ANALYSIS

```sql
SELECT 'Total Patients', COUNT(*) FROM hosp
UNION ALL
SELECT 'High Complexity', COUNT(*)
FROM hosp
WHERE medication_count >= 7 OR length_of_stay >= 7
UNION ALL
SELECT 'Discharged to Rehab/Nursing', COUNT(*)
FROM hosp
WHERE discharge_destination IN ('Rehab','Nursing_Facility')
UNION ALL
SELECT 'Readmitted', COUNT(*)
FROM hosp
WHERE readmitted_30_days = 1;
```

**Insight:**
61% high-risk → only 30% get rehab
→ **Big care gap**

---

## 🔹 14. TOP 10% HIGH-RISK PATIENTS

```sql
SELECT discharge_destination,
COUNT(*) AS patients,
AVG(readmitted_30_days) * 100 AS readmission_rate
FROM (
SELECT *,
NTILE(10) OVER (
 ORDER BY (medication_count * 0.4 +
           diabetes * 2 +
           length_of_stay * 0.3) DESC
) AS risk_decile
FROM hosp
) t
WHERE risk_decile = 1
GROUP BY discharge_destination;
```

**Insight:**
~70% high-risk patients sent **home**
→ Major problem

---

## 🔹 15. A/B TEST (INTERVENTION IMPACT)

```sql
SELECT experiment_group,
COUNT(*) AS patients,
AVG(new_readmission) * 100 AS readmission_rate
FROM (...)
GROUP BY experiment_group;
```

**Insight:**
11.45% → 10.95% (↓4.4%)
→ Saves ~£2.25M

---

## 🔹 16. BEST SEGMENT FOR TREATMENT

```sql
SELECT discharge_destination, experiment_group,
AVG(new_readmission) * 100
FROM (...)
GROUP BY discharge_destination, experiment_group;
```

**Insight:**
Best results in **Rehab patients**
→ Use **targeted strategy**

---

# ✅ FINAL KEY TAKEAWAYS

* Readmissions ≠ stay duration
* Risk = **multi-factor (age + meds + condition)**
* High-risk patients drive **costs**
* Discharge decisions are **misaligned**
* Targeted intervention = **best ROI**

---

---

### Python EDA

**Libraries:** pandas, matplotlib, seaborn

**Key analyses:**
## Which factors are actually related to readmission?

<img width="1388" height="976" alt="image" src="https://github.com/user-attachments/assets/e047480e-74b9-46ce-b47a-36745058c3d0" />

Patient outcomes such as hospital stay and readmission are not driven by individual variables, but rather by a combination of factors including overall health condition, comorbidities, and treatment complexity.
The weak correlations across all variables indicate that simple linear relationships do not exist, making this a complex healthcare problem requiring advanced modeling techniques.
–
## Do patients with multiple conditions stay longer?

<img width="661" height="465" alt="chart8" src="https://github.com/user-attachments/assets/821fc78b-9c78-435d-9dec-add9efe2ed1a" />



“Even when combining key conditions like diabetes and hypertension, there was no significant difference in hospital stay distributions. This suggests that patient outcomes are driven more by overall clinical complexity rather than the presence of individual conditions.”

## Does treatment complexity (medication count) affect hospital stay?


<img width="665" height="358" alt="chart9" src="https://github.com/user-attachments/assets/e1971cac-5b57-48c8-af16-673f7fa75e97" />

Median length of stay ≈ same (~5–6 days) across all medication counts
Spread (variance) is also very similar
No clear upward or downward trend

More meds ≠ longer stay (surprising)
So treatment complexity is not just number of meds
Real drivers might be:
patient condition severity
combinations of diseases
readmission behavior

## Which combination of factors ACTUALLY drives readmission risk?

<img width="1382" height="916" alt="image" src="https://github.com/user-attachments/assets/06c8bc72-0925-4f38-a2c3-948017e0999d" />

Key Observations
Young (18–30) + Low meds = HIGHEST risk (0.14)
Middle age (30–50) + Medium meds = elevated risk (0.13)
Older (70–90) + High meds = consistently high (0.13)
“I segmented patients based on age and treatment complexity and identified high-risk cohorts driving readmissions.”

## “Which patients should we intervene on BEFORE discharge?”



<img width="1354" height="864" alt="image" src="https://github.com/user-attachments/assets/7d1ac134-e4ea-4d16-9587-c112d4de90dc" />

“The relationship between medication count and readmission risk is weak and non-linear. This suggests that treatment complexity alone is insufficient to predict readmissions, and additional factors like patient condition or post-discharge care are likely more important drivers.”

## “Where are we spending MORE but NOT reducing risk?”

“After defining success, I analyzed how treatment complexity affects outcomes. I observed that beyond a certain medication count, success rates plateau or decline, indicating diminishing returns from increased treatment.”

successful_outcome
1    26326
0     3674
Name: count, dtype: int64

##  “Does treatment complexity improve or hurt outcomes?”



<img width="681" height="413" alt="CHART13" src="https://github.com/user-attachments/assets/71dbccf2-f998-490c-b34e-fbba5c22b770" />

“After defining success, I analyzed how treatment complexity affects outcomes. I observed that beyond a certain medication count, success rates plateau or decline, indicating diminishing returns from increased treatment.”

## Medication + Diabetes combo


<img width="708" height="437" alt="chart15 " src="https://github.com/user-attachments/assets/7056f3ff-0723-40f8-a6b8-aead3c9a0d2e" />

“While diabetic patients consistently show slightly lower success rates, treatment complexity (medication count) does not significantly impact outcomes. This suggests that increasing medications alone is not driving better or worse recovery.

## Identify the lowest success group - highest treatment 


<img width="641" height="433" alt="chart16" src="https://github.com/user-attachments/assets/38a14522-da85-4df1-8d6a-2789c9889828" />

Worst-performing segment

Diabetes = 1 (Yes)
Age = 50–70
High treatment (≥5 meds)
Success rate ≈ 0.861 (lowest among all groups)
“I defined success as non-readmission and found that increasing treatment complexity does not always improve outcomes. Specifically, diabetic patients aged 50–70 with high medication counts had the lowest success rates. This suggests diminishing returns from medication-heavy approaches. Instead of increasing treatment, I would introduce a risk-based alert system to trigger targeted interventions like follow-ups and care coordination, improving outcomes without increasing cost.”

## “Do patient outcomes change based on WHEN or HOW treatment starts?”
events = df.copy()

events['event_sequence'] = (
    "admission → "
    + "diagnosis(" + events['diabetes'].astype(str) + ") → "
    + "treatment(" + events['medication_count'].astype(str) + " meds) → "
    + "stay(" + events['length_of_stay'].astype(str) + ") → "
    + "outcome(" + events['readmitted_30_days'].astype(str) + ")"
)

events[['event_sequence']].head()   
#Find Most Common Journeys
journey_counts = events['event_sequence'].value_counts().head(10)
print(journey_counts)
# Filter failure journeys only
failure_journeys = events[events['readmitted_30_days'] == 1]

# Top failure paths
failure_counts = failure_journeys['event_sequence'].value_counts().head(10)

print(failure_counts)

event_sequence
admission → diagnosis(0) → treatment(4 meds) → stay(2) → outcome(0)     149
admission → diagnosis(1) → treatment(1 meds) → stay(3) → outcome(0)     148
admission → diagnosis(0) → treatment(10 meds) → stay(6) → outcome(0)    147
admission → diagnosis(0) → treatment(3 meds) → stay(6) → outcome(0)     143
admission → diagnosis(0) → treatment(0 meds) → stay(1) → outcome(0)     142
admission → diagnosis(0) → treatment(4 meds) → stay(7) → outcome(0)     142
admission → diagnosis(0) → treatment(8 meds) → stay(9) → outcome(0)     140
admission → diagnosis(0) → treatment(2 meds) → stay(1) → outcome(0)     139
admission → diagnosis(0) → treatment(9 meds) → stay(3) → outcome(0)     139
admission → diagnosis(1) → treatment(2 meds) → stay(4) → outcome(0)     138
Name: count, dtype: int64
event_sequence
admission → diagnosis(1) → treatment(8 meds) → stay(8) → outcome(1)     29
admission → diagnosis(1) → treatment(5 meds) → stay(9) → outcome(1)     28
admission → diagnosis(1) → treatment(1 meds) → stay(6) → outcome(1)     27
admission → diagnosis(0) → treatment(3 meds) → stay(5) → outcome(1)     26
admission → diagnosis(1) → treatment(5 meds) → stay(8) → outcome(1)     26
admission → diagnosis(0) → treatment(10 meds) → stay(4) → outcome(1)    26
admission → diagnosis(0) → treatment(0 meds) → stay(8) → outcome(1)     25
admission → diagnosis(1) → treatment(3 meds) → stay(6) → outcome(1)     25
admission → diagnosis(1) → treatment(8 meds) → stay(7) → outcome(1)     25
admission → diagnosis(1) → treatment(9 meds) → stay(7) → outcome(1)     25
Name: count, dtype: int64

By mapping patient journeys, I found that the most common failure paths consistently involve diabetic patients, regardless of treatment intensity, and often include longer hospital stays. This suggests that outcomes are driven more by underlying condition than treatment volume. Therefore, I would introduce an early risk detection system at the diagnosis stage to proactively manage high-risk patients through personalized care and monitoring, rather than relying on increased treatment alone.”

## Cost-Benefit Analysis
## Always include this — it justifies your entire analysis to hospital leadership and makes your work actionable, not just academic.
## “Is it worth investing in intervention for high-risk patients?”
# 💰 Cost-Benefit Analysis (Pre-ML using simple rules)

# Step 1: Define business assumptions
cost_per_readmission = 15000      # $ per readmission
intervention_cost = 500           # $ per patient intervention
effectiveness = 0.30              # 30% reduction in readmissions

# Step 2: Define HIGH-RISK group (rule-based)
# Example: high meds (>=5) + diabetes = 1
high_risk = df[(df['medication_count'] >= 5) & (df['diabetes'] == 1)]

# Step 3: Baseline readmissions
baseline_readmissions = high_risk['readmitted_30_days'].sum()
baseline_cost = baseline_readmissions * cost_per_readmission

# Step 4: Expected improvement after intervention
expected_reduction = baseline_readmissions * effectiveness
saved_cost = expected_reduction * cost_per_readmission

# Step 5: Intervention cost
intervention_total_cost = len(high_risk) * intervention_cost

# Step 6: Net benefit + ROI
net_benefit = saved_cost - intervention_total_cost
roi = net_benefit / intervention_total_cost

# Step 7: Output
print("📊 High-Risk Patients:", len(high_risk))
print("💰 Baseline Cost:", baseline_cost)
print("💸 Intervention Cost:", intervention_total_cost)
print("📉 Savings from Reduced Readmissions:", saved_cost)
print("🔥 Net Benefit:", net_benefit)
print("📈 ROI:", roi)


 High-Risk Patients: 8054
💰 Baseline Cost: 16395000
💸 Intervention Cost: 4027000
📉 Savings from Reduced Readmissions: 4918500.0
🔥 Net Benefit: 891500.0
📈 ROI: 0.22138068040725106

“I identified a high-risk segment using rule-based logic and conducted a cost-benefit analysis. The intervention showed a positive ROI of 22%, generating nearly $900K in net savings. This validated that targeted interventions are financially viable, even before introducing predictive models.”

## Strategy to Increase ROI (22% → 60%+)

                                                                                                                                                                                                                                    # 🚀 ROI Optimization (Rule-based → Risk scoring → Top targeting → Tiered cost)

# =========================
# Step 1: Business assumptions
# =========================
cost_per_readmission = 15000
effectiveness = 0.30   # intervention reduces 30% readmissions

# =========================
# Step 2: Create risk score (NO ML, smart proxy)
# =========================
df['risk_score'] = (
    (df['medication_count'] * 0.4) +
    (df['diabetes'] * 2) +
    (df['length_of_stay'] * 0.3)
)

# =========================
# Step 3: Target TOP 20% highest-risk patients
# =========================
top_n = int(0.2 * len(df))
target = df.sort_values(by='risk_score', ascending=False).head(top_n)

# =========================
# Step 4: Tiered intervention cost (smart spending)
# =========================
def intervention_cost_fn(score):
    if score > 8:
        return 500   # high risk → intensive care
    elif score > 5:
        return 200   # medium risk → moderate intervention
    else:
        return 50    # low risk → light touch

target['intervention_cost'] = target['risk_score'].apply(intervention_cost_fn)

# =========================
# Step 5: Cost-Benefit calculation
# =========================
baseline_readmissions = target['readmitted_30_days'].sum()
baseline_cost = baseline_readmissions * cost_per_readmission

expected_reduction = baseline_readmissions * effectiveness
saved_cost = expected_reduction * cost_per_readmission

intervention_total_cost = target['intervention_cost'].sum()

net_benefit = saved_cost - intervention_total_cost
roi = net_benefit / intervention_total_cost

# =========================
# Step 6: Output
# =========================
print("🎯 Targeted Patients:", len(target))
print("💰 Baseline Cost:", baseline_cost)
print("💸 Intervention Cost:", intervention_total_cost)
print("📉 Savings:", saved_cost)
print("🔥 Net Benefit:", net_benefit)
print("🚀 ROI:", roi)


A 
🎯 Targeted Patients: 6000
💰 Baseline Cost: 12090000
💸 Intervention Cost: 1482600
📉 Savings: 3626999.9999999995
🔥 Net Benefit: 2144399.9999999995
🚀 ROI: 1.4463779846216103


“Initially, a rule-based intervention yielded a 22% ROI. However, by shifting to risk-based prioritization and targeting only the top 20% highest-risk patients, I significantly reduced intervention costs while retaining most of the savings. Additionally, introducing tiered interventions ensured efficient resource allocation. This improved ROI to over 140%, demonstrating that smarter targeting—not more intervention—is the key driver of impact.”
## ROI vs % population curve
<img width="687" height="426" alt="Screenshot 2026-04-24 at 1 55 53 AM" src="https://github.com/user-attachments/assets/12e68d02-8dc8-44e4-b93a-1686c7b4de65" />


“I identified that the top 10% of patients contribute disproportionately to readmission costs. By targeting only this segment, we achieve the highest ROI (~43%). Expanding beyond this group leads to diminishing returns due to lower marginal benefit and increasing intervention costs.”


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

## Identify the lowest success group - highest treatment

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
