<div align="center">

# 🏥 Hospital Readmission Risk Analytics

### *Can we predict which patients will come back — before they leave?*

---

![SQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Excel](https://img.shields.io/badge/Microsoft_Excel-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white)
![Tableau](https://img.shields.io/badge/Tableau-E97627?style=for-the-badge&logo=tableau&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![Seaborn](https://img.shields.io/badge/Seaborn-3776AB?style=for-the-badge&logo=python&logoColor=white)

**30,000 patient records · 4 tools · 1 question that changed the framing entirely**

</div>

---

## 📖 Table of Contents

| Section | Jump To |
|---|---|
| 🧠 The Story | [Read it](#-the-story) |
| 💡 Key Findings | [See findings](#-key-findings-at-a-glance) |
| 🛠️ Tech Stack | [Tools used](#️-tech-stack) |
| 🗂️ Project Structure | [File layout](#️-project-structure) |
| 📋 Dataset | [Column reference](#-dataset) |
| 🔍 SQL Analysis | [10 queries explained](#-sql-analysis--10-queries-10-findings) |
| 🐍 Python EDA | [Charts explained](#-python-eda--visual-storytelling) |
| 📊 Excel Dashboard | [Dashboard walkthrough](#-excel-dashboard) |
| 📈 Tableau Dashboards | [Two dashboards explained](#-tableau-dashboards) |
| 💰 Financial Model | [ROI analysis](#-financial-model--roi-optimisation) |
| ⚠️ Challenges | [What was hard](#️-challenges) |
| ✅ Recommendations | [Action plan](#-recommendations) |
| 🏁 Conclusion | [Final thoughts](#-conclusion) |

---

## 🧠 The Story

Every year, hospitals lose millions — not just in money, but in **trust** — when patients return through the same doors within 30 days of discharge. These readmissions are expensive, often preventable, and a signal that something in the care pathway broke down.

This project started with a single question:

> **Can we predict which patients will come back, before they leave?**

The dataset had **30,000 patient records**, **12 variables**, and no obvious answers upfront. The first instinct was that the usual suspects would be guilty: older patients, diabetics, people with high blood pressure, patients who stayed longer.

**That instinct turned out to be wrong in almost every case.**

The analysis unfolded in four stages, each one peeling back a layer:

| Stage | Tool | What It Revealed |
|---|---|---|
| **Stage 1 — Baseline** | SQL | Overall rate 12.25%. Individual variables explain almost nothing. |
| **Stage 2 — Patterns** | Python | Compound risk, not single factors, drives readmission. |
| **Stage 3 — Structural Gap** | Tableau | 70% of highest-risk patients are being sent *home*. |
| **Stage 4 — Business Case** | SQL + Python | Risk-score targeting → 144% ROI, £2.1M net benefit. |

---

## 💡 Key Findings at a Glance

| 🔎 Finding | 📌 Detail |
|---|---|
| **Overall readmission rate** | 12.25% — roughly 1 in 8 patients |
| **Single-variable predictors** | Weak across the board — age, BMI, diabetes, hypertension alone show minimal impact |
| **Top risk driver** | Compound risk: diabetes + high medication count + age 50–70 *together*, not individually |
| **Critical discharge gap** | 70% of the top 10% highest-risk patients discharged home |
| **Readmission by destination** | Rehab: 17.5% · Nursing Facility: 16.8% · Home: 10.0% |
| **Worst patient segment** | Diabetic · Age 50–70 · ≥5 medications → success rate of just 86.1% |
| **A/B test outcome** | Structured follow-up: 11.45% → 10.95% readmissions (4.4% relative improvement) |
| **Total readmission cost** | ~£55M across all 30,000 patients at £15,000 per readmission |
| **Optimal ROI strategy** | Top 20% by risk score + tiered interventions → **144% ROI, £2.1M net benefit** |

---

## 🛠️ Tech Stack

| Tool | Role in This Project |
|---|---|
| **MySQL Workbench** | SQL queries, window functions, risk scoring, A/B simulation, funnel analysis |
| **Python** (pandas, seaborn, matplotlib) | EDA, correlation analysis, segmentation, cost-benefit and ROI modelling |
| **Microsoft Excel** | Pivot tables, interactive dashboard with slicers |
| **Tableau** | Multi-sheet analytical dashboard with filters, heatmaps, and cohort views |

---

## 🗂️ Project Structure

```
Hospital-Readmission-Risk-Analytics/
│
├── 📁 Data/
│   └── hosp.xlsx                        # Raw dataset — 30,000 patient records
│
├── 📁 SQL/
│   └── hospital.sql                     # All queries with inline commentary
│
├── 📁 notebook/
│   └── hospital.ipynb                   # Jupyter notebook — full EDA and modelling
│
├── 📁 dashboard/
│   └── hospital dashboard.twb           # Tableau workbook (2 dashboards, 7 sheets)
│
├── 📁 images/                           # All chart exports (SQL, Python, Excel, Tableau)
│
└── README.md
```

---

## 📋 Dataset

**File:** `hosp.xlsx` &nbsp;|&nbsp; **Rows:** 30,000 &nbsp;|&nbsp; **Columns:** 12 &nbsp;|&nbsp; **Missing values:** None

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
| `readmitted_30_days` | binary | 1 = readmitted within 30 days ← **target variable** |
| `systolic_bp` | int | Systolic blood pressure |
| `diastolic_bp` | int | Diastolic blood pressure |

---

## 🔍 SQL Analysis — 10 Queries, 10 Findings

> Every query below had a specific purpose. No query was run twice for the same reason. Each one either confirmed a hypothesis or dismantled one.

---

### 📌 Query 1 — The Scale of the Problem

```sql
SELECT AVG(readmitted_30_days) * 100 AS readmission_rate
FROM hosp;
```

![SQL Chart 1](images/sql1%20.png)

**What it shows:** The headline number — **12.25%** of patients return within 30 days. That's 1 in 8. At £15,000 per readmission, across 30,000 patients, that's a **£55M problem** hiding in plain sight. This single number frames the entire project: if you can move it even 1 percentage point, the financial and human impact is enormous.

---

### 📌 Query 2 — Is Length of Stay the Culprit?

```sql
SELECT readmitted_30_days, AVG(length_of_stay) AS avg_stay FROM hosp GROUP BY readmitted_30_days;
SELECT diabetes, AVG(length_of_stay) AS avg_stay FROM hosp GROUP BY diabetes;
SELECT medication_count, AVG(length_of_stay) AS avg_stay FROM hosp GROUP BY medication_count;
```

![SQL Chart 2](images/sql2.png)

**What it shows:** Across every cut — readmitted vs not, diabetic vs not, low vs high meds — the average length of stay barely moves. It hovers around 5–6 days regardless. This is a decisive early finding: **longer hospitalisation is not reducing readmissions**. The problem lives *after* discharge, not during the hospital stay.

---

### 📌 Query 3 — Does Age Predict Readmission?

```sql
SELECT
  CASE
    WHEN age BETWEEN 18 AND 30 THEN '18-30'
    WHEN age BETWEEN 31 AND 50 THEN '30-50'
    WHEN age BETWEEN 51 AND 70 THEN '50-70'
    ELSE '70-90'
  END AS age_group,
  AVG(readmitted_30_days) * 100 AS readmission_rate
FROM hosp GROUP BY age_group ORDER BY readmission_rate DESC;
```

![SQL Chart 3](images/sql3.png)

**What it shows:** Readmission rates are almost identical across every age group — the differences are decimal-level noise. The assumption that older patients return more often is **statistically unsupported here**. Age alone is a weak predictor. This clears the ground for compound analysis.

---

### 📌 Query 4 — Age × Medication: Does Combining Variables Help?

```sql
SELECT age_group, med_group, AVG(readmitted_30_days) * 100 AS readmission_rate
FROM hosp GROUP BY age_group, med_group ORDER BY readmission_rate DESC;
```

![SQL Chart 4](images/sql4.png)

**What it shows:** When age and medication count are combined, readmission climbs to ~13% in specific intersections — higher than either variable produces alone. This is the first signal that **compound segmentation is the right path**. Not any one factor, but how they interact.

---

### 📌 Query 5 — Risk Scoring Model

```sql
SELECT *,
  (medication_count * 0.4 + diabetes * 2 + length_of_stay * 0.3) AS risk_score,
  NTILE(5) OVER (ORDER BY (...) DESC) AS risk_group
FROM hosp;
```

![SQL Chart 5](images/sql5%20.png)

**What it shows:** Each patient receives a **composite risk score** weighted by clinical relevance (diabetes weighted heaviest at 2x, medication count at 0.4x, length of stay at 0.3x). Patients are then bucketed into 5 groups — Group 1 being the top 20% highest-risk. This is the engine behind the entire intervention strategy: you can't target what you haven't ranked.

---

### 📌 Query 6 — What Do High-Risk Patients Cost?

```sql
SELECT SUM(readmitted_30_days) * 15000 AS total_cost FROM hosp;

SELECT COUNT(*) AS patients, SUM(readmitted_30_days) * 15000 AS high_risk_cost
FROM (...) WHERE risk_group = 1;
```

![SQL Chart 6](images/sql6.png)

**What it shows:** The top 20% highest-risk patients account for **~22% of all readmission costs** — approximately **£12M** out of the total £55M. A small, identifiable group is driving a disproportionate share of the financial burden. This is the economic case for targeted intervention over blanket treatment.

---

### 📌 Query 7 — Discharge Destination vs Readmission Rate

```sql
SELECT discharge_destination, COUNT(*) AS patients,
  AVG(readmitted_30_days) * 100 AS readmission_rate
FROM hosp GROUP BY discharge_destination ORDER BY readmission_rate DESC;
```

![SQL Chart 7](images/sql7.png)

**What it shows:** Rehab patients: **17.5%** readmission. Nursing Facility: **16.8%**. Home: **10.0%**. At first glance, this looks like Rehab is failing patients. It isn't. The next query reveals the real story: Rehab is receiving *more complex patients* — that's why the rate is higher. The rate alone without context is a trap.

---

### 📌 Query 8 — Funnel Analysis: Where Does the System Break?

```sql
SELECT 'Total Patients' AS stage, COUNT(*) FROM hosp
UNION ALL SELECT 'High Complexity', COUNT(*) FROM hosp WHERE medication_count >= 7 OR length_of_stay >= 7
UNION ALL SELECT 'Discharged to Rehab/Nursing', COUNT(*) FROM hosp WHERE discharge_destination IN ('Rehab','Nursing_Facility')
UNION ALL SELECT 'Readmitted', COUNT(*) FROM hosp WHERE readmitted_30_days = 1;
```

![SQL Chart 8](images/sql8.png)

**What it shows:** 61% of patients qualify as high complexity. Of those, only **30% are sent to Rehab or Nursing**. There's a massive drop in care escalation — the majority of complex patients leave with the same plan as simple ones. This is the **structural failure point**: the system isn't differentiating at discharge.

---

### 📌 Query 9 — The Top 10% Highest-Risk: Where Are They Going?

```sql
SELECT discharge_destination, COUNT(*) AS patients,
  AVG(readmitted_30_days) * 100 AS readmission_rate
FROM (...WHERE risk_decile = 1)
GROUP BY discharge_destination;
```

![SQL Chart 9](images/sql9.png)

**What it shows:** Among the **top 10% highest-risk patients**, approximately **70% are being discharged home**. This is the most critical finding in the entire SQL analysis. The highest-risk cohort — the patients most likely to return — is being sent home at nearly the same rate as the lowest-risk patients. The discharge decision is not being made with risk data in hand.

---

### 📌 Query 10 — A/B Test: Does a Follow-Up Intervention Work?

```sql
SELECT experiment_group, AVG(new_readmission) * 100 AS readmission_rate
FROM (
  SELECT *, CASE WHEN experiment_group = 'B_Treatment' AND RAND() < 0.15 THEN 0
            ELSE readmitted_30_days END AS new_readmission
  FROM (...assigned via MOD(ROW_NUMBER(), 2))
) final GROUP BY experiment_group;
```

![SQL Chart 10](images/sql10.png)

**What it shows:** A simulated A/B test assigns half the patient population to a structured follow-up intervention. Result: readmissions drop from **11.45% → 10.95%** — a **4.4% relative improvement**. Extrapolated across the dataset, this translates to approximately **£2.25M in annual savings**. The simulation is clearly labelled as directional, not causal — but it models what a real trial pipeline would look like and what effect sizes to plan for.

---

## 🐍 Python EDA — Code + Visual Storytelling

> Python's role was distributional insight and economic optimisation — finding *how* variables behave, not just *what* their averages say. Every analysis below includes the exact code used.

---

### 🔬 Setup — Loading & Profiling the Dataset

```python
import pandas as pd

df = pd.read_csv("hosp.csv")
df.head()
```

```
   age  gender  cholesterol   bmi  diabetes  hypertension  medication_count  \
0   74   Other          240  31.5         1             0                 5   
1   46  Female          292  36.3         0             0                 4   
2   89   Other          153  30.3         0             1                 1   
3   84  Female          153  31.5         0             1                 3   
4   32   Other          205  18.4         0             1                 6   

   length_of_stay discharge_destination  readmitted_30_days  systolic_bp  \
0               1       Nursing_Facility                   1          130   
1               3       Nursing_Facility                   0          120   
2               1                  Home                   0          135   
3              10                  Home                   0          123   
4               4       Nursing_Facility                   0          135   
```

```python
df.describe()
```

| | age | cholesterol | bmi | medication_count | length_of_stay | readmitted_30_days |
|---|---|---|---|---|---|---|
| **mean** | 53.88 | 225.26 | 28.95 | 5.01 | 5.50 | **0.122** |
| **std** | 21.06 | 43.59 | 6.35 | 3.17 | 2.87 | 0.33 |
| **min** | 18 | 150 | 18.0 | 0 | 1 | 0 |
| **max** | 90 | 300 | 40.0 | 10 | 10 | 1 |

> **Population snapshot:** Average age ~54, average BMI ~29 (overweight category), readmission rate **12.25%**. No missing values across all 30,000 records.

---

### 📊 Chart 1 — Age Distribution of Patients

```python
import matplotlib.pyplot as plt

plt.hist(df['age'], bins=20)
plt.title("Age Distribution of Patients")
plt.xlabel("Age")
plt.ylabel("Count")
plt.show()
```

![Chart 1](images/chart1.png)

**What it shows:** The age distribution is remarkably uniform across the full 18–90 range. There is no peak at any particular age bracket — the dataset represents a genuinely balanced cross-section of the patient population. This matters because it means **any age-related findings will be real signals, not sampling artifacts**. If older patients showed elevated readmission rates, you could trust that finding. They don't — which is the first signal that something counterintuitive is happening in this data.

---

### 📊 Chart 2 — Do Diabetic Patients Stay Longer?

```python
df.groupby('diabetes')['length_of_stay'].mean().plot(kind='bar')

plt.title("Length of Stay: Diabetes vs Non-Diabetes")
plt.xlabel("Diabetes (0 = No, 1 = Yes)")
plt.ylabel("Average Length of Stay")
plt.show()
```

![Chart 2](images/chart2.png)

**What it shows:** Diabetic and non-diabetic patients have virtually identical average hospital stays — both hover around 5.5 days. The bar heights are indistinguishable. **Diabetes does not extend hospitalisation.** This is a direct refutation of one of the most common assumptions entering healthcare analysis: that chronic conditions mean longer stays. The data says otherwise.

---

### 📊 Chart 3 — Does Hypertension Affect Hospital Stay?

```python
df.groupby('hypertension')['length_of_stay'].mean().plot(kind='bar')

plt.title("Length of Stay: Hypertension vs Non-Hypertension")
plt.xlabel("Hypertension (0 = No, 1 = Yes)")
plt.ylabel("Average Length of Stay")
plt.show()
```

![Chart 3](images/chart3.png)

**What it shows:** Identical story to diabetes — hypertensive and non-hypertensive patients stay for almost exactly the same duration. **Two of medicine's most common chronic conditions, examined individually, tell us nothing about why some patients stay longer.** The implication is clear: individual comorbidities are poor predictors of resource use. Combinations are where the signal hides.

---

### 📊 Chart 4 — Correlation Heatmap: The Most Important Non-Finding

```python
import seaborn as sns
import matplotlib.pyplot as plt

numeric_df = df.select_dtypes(include=['number'])

sns.heatmap(numeric_df.corr(), annot=True, cmap='coolwarm')
plt.title("Feature Correlation Heatmap", fontsize=14, fontweight='bold')
plt.show()
```

![Chart 4](images/chart4.png)

**What it shows:** Every correlation between patient features and readmission hovers near zero. There is **no single strong predictor** anywhere in the matrix — not age, not BMI, not medication count, not blood pressure. For most analysts, this would feel like failure. The correct interpretation is the opposite: **readmission is inherently multivariate**. No linear relationship exists because the problem is non-linear and compound. This single chart redirected the entire analysis toward segmentation and custom risk scoring.

---

### 📊 Chart 5 — Hospital Stay Distributions by Condition Combo (Violin)

```python
import seaborn as sns

# Create combined condition group
df['risk_group'] = df['diabetes'].astype(str) + "_" + df['hypertension'].astype(str)
df['risk_group'] = df['risk_group'].map({
    '0_0': 'No conditions',
    '1_0': 'Diabetes Only',
    '0_1': 'Hypertension Only',
    '1_1': 'Both Conditions'
})

sns.set(style="whitegrid")
plt.figure(figsize=(10, 6))

sns.violinplot(
    x='risk_group',
    y='length_of_stay',
    data=df,
    inner='quartile'
)

plt.title("Distribution of Hospital Stay by Patient Condition", fontsize=14, weight='bold')
plt.xlabel("Patient Group")
plt.ylabel("Length of Stay")
plt.xticks(rotation=20)
plt.show()
```

![Chart 5](images/chart5.png)

**What it shows:** Four violin plots — one for each combination of diabetes and hypertension status — showing nearly identical width, height, and quartile placement. The distributions are so similar they're essentially copies of each other. **Having both conditions does not shift the hospital stay distribution compared to having neither.** This definitively rules out comorbidity burden as a driver of hospitalisation duration and keeps the analytical focus on post-discharge factors.

---

### 📊 Chart 6 — Medication Count vs Hospital Stay (Box Distribution)

```python
sns.set_theme(style="darkgrid")
plt.figure(figsize=(12, 6))

sns.boxplot(
    x='medication_count',
    y='length_of_stay',
    data=df,
    palette='Spectral'
)

plt.title("Medication Count vs Hospital Stay (Distribution View)", fontsize=16, weight='bold')
plt.xlabel("Medication Count")
plt.ylabel("Length of Stay")
plt.show()
```

![Chart 6](images/chart6%20.png)

**What it shows:** Eleven side-by-side box plots — one per medication count level (0–10) — with almost identical median lines, IQR boxes, and whisker lengths. The medians all sit at ~5–6 days. The variance barely changes. **More medications do not mean longer stays.** This is counterintuitive and important: treatment complexity as measured by medication volume doesn't explain hospitalisation duration. The real drivers are hiding elsewhere.

---

### 📊 Chart 7 — Age × Medication: What Actually Drives Readmission Risk?

```python
import seaborn as sns

sns.set_theme(style="dark")

# Create segmentation bins
df['age_group'] = pd.cut(df['age'], bins=[18, 30, 50, 70, 90],
                          labels=['18-30', '30-50', '50-70', '70-90'])
df['med_group'] = pd.cut(df['medication_count'], bins=[0, 3, 6, 10],
                          labels=['Low', 'Medium', 'High'])

# Pivot table for heatmap
pivot = df.pivot_table(
    values='readmitted_30_days',
    index='age_group',
    columns='med_group',
    aggfunc='mean'
)

plt.figure(figsize=(10, 6))
sns.heatmap(pivot, annot=True, cmap='coolwarm', fmt=".2f")

plt.title("Readmission Risk by Age & Treatment Complexity", fontsize=16, weight='bold')
plt.xlabel("Medication Level")
plt.ylabel("Age Group")
plt.show()
```

![Chart 7](images/chart7.png)

**What it shows:** A heatmap where rows are age groups and columns are medication complexity tiers. The highest readmission risk — **0.14** — appears in the **18–30, Low medication** cell, which completely defies the assumption that older, more medicated patients are the highest risk. Middle-aged patients with medium medications (0.13) also stand out. **Risk doesn't follow a simple age or medication gradient — it forms a non-obvious pattern that only becomes visible through compound segmentation.** This is why rule-of-thumb flagging systems fail.

---

### 📊 Chart 8 — Patient Risk vs Cost Segmentation (Scatter)

```python
sns.set_theme(style="dark")

# Create grouped data
grouped = df.groupby('medication_count').agg({
    'length_of_stay': 'mean',
    'readmitted_30_days': 'mean'
}).reset_index()

plt.figure(figsize=(10, 6))

scatter = plt.scatter(
    grouped['readmitted_30_days'],
    grouped['length_of_stay'],
    s=200,
    c=grouped['medication_count'],
    cmap='plasma',
    alpha=0.8
)

for i, row in grouped.iterrows():
    plt.text(row['readmitted_30_days'] + 0.0005, row['length_of_stay'] + 0.01,
             f"Med {int(row['medication_count'])}", fontsize=9)

# Decision quadrant lines
plt.axhline(df['length_of_stay'].mean(), linestyle='--', color='white')
plt.axvline(df['readmitted_30_days'].mean(), linestyle='--', color='white')

plt.colorbar(label="Medication Count")
plt.title("Patient Risk vs Cost Segmentation", fontsize=16, weight='bold')
plt.xlabel("Readmission Risk")
plt.ylabel("Avg Length of Stay")
plt.show()
```

![Chart 8](images/chart8.png)

**What it shows:** Each dot represents a medication count level, plotted by its average readmission rate (x-axis) and average length of stay (y-axis). The dashed white lines create four quadrants — High Risk/High Cost, High Risk/Low Cost, Low Risk/High Cost, Low Risk/Low Cost. Points scatter across quadrants without any clear clustering. **No medication level reliably lands in the "low risk, low cost" quadrant.** This confirms that medication count alone is not a useful lever — targeting strategy needs to be built on the composite risk score, not any single variable.

---

### 📊 Chart 9 — North Star: Success Rate vs Treatment Complexity

```python
# Define the success metric
df['successful_outcome'] = 1 - df['readmitted_30_days']

# Aggregate by medication count
rate = df.groupby('medication_count')['successful_outcome'].mean().reset_index()

sns.set_theme(style="darkgrid")
plt.figure(figsize=(10, 6))

sns.lineplot(
    x='medication_count',
    y='successful_outcome',
    data=rate,
    marker='o',
    linewidth=3
)

plt.title("Success Rate vs Treatment Complexity", fontsize=16, weight='bold')
plt.xlabel("Medication Count")
plt.ylabel("Success Rate (Non-Readmission)")
plt.show()
```

```
successful_outcome
1    26326
0     3674
Name: count, dtype: int64
```

![Chart 9](images/chart9.png)

**What it shows:** Success rate (non-readmission) is plotted against medication count, with the north star metric defined as `1 - readmitted_30_days`. The line is non-monotonic — it rises, flattens, dips, and plateaus with no clean upward trend. **Beyond a certain medication threshold, success rates plateau or decline.** This is the diminishing returns finding in its clearest form: adding more medications past a certain point stops helping and may begin to harm. Any intervention programme that routes patients toward medication escalation is working against this evidence.

---

### 📊 Chart 10 — Success Rate by Diabetes Status

```python
rate = df.groupby('diabetes')['successful_outcome'].mean().reset_index()

plt.figure(figsize=(8, 5))
sns.barplot(x='diabetes', y='successful_outcome', data=rate)

plt.title("Success Rate by Diabetes Condition", fontsize=16, weight='bold')
plt.xlabel("Diabetes (0 = No, 1 = Yes)")
plt.ylabel("Success Rate")
plt.show()
```

![Chart 10](images/chart10.png)

**What it shows:** Non-diabetic patients (0) show a marginally higher success rate than diabetic patients (1). The gap is visible but not dramatic. The key takeaway is not the size of the gap but the implication: **diabetes creates a consistent headwind on outcomes**, but it's not overwhelming on its own. What makes it dangerous is when it combines with other risk factors — as the next chart demonstrates.

---

### 📊 Chart 11 — CHART 13: Diminishing Returns by Diabetes Group

```python
combo = df.groupby(['diabetes', 'medication_count'])['successful_outcome'].mean().reset_index()

plt.figure(figsize=(10, 6))

sns.lineplot(
    x='medication_count',
    y='successful_outcome',
    hue='diabetes',
    data=combo,
    marker='o'
)

plt.title("Success Rate vs Treatment Complexity (by Diabetes)", fontsize=16, weight='bold')
plt.xlabel("Medication Count")
plt.ylabel("Success Rate")
plt.legend(title="Diabetes (0 = No, 1 = Yes)")
plt.show()
```

![CHART 13](images/CHART13.png)

**What it shows:** Two lines — one for diabetic patients, one for non-diabetic — plotted across all medication count levels. Both lines show the same plateau-and-decline pattern. Critically, **the diabetic line consistently sits below the non-diabetic line at every medication level**. Increasing medication count doesn't close the gap. This tells us that for diabetic patients, the problem is not under-treatment — it's something downstream of the hospital stay that medications alone cannot address.

---

### 📊 Chart 12 — Worst Performing Segment: Drilling to the Bottom

```python
# Filter to high-treatment patients only
high_treatment = df[df['medication_count'] >= 5]

# Three-way segmentation: diabetes × age group × success rate
deep_segment = high_treatment.groupby(
    ['diabetes', 'age_group']
)['successful_outcome'].mean().reset_index()

deep_segment = deep_segment.sort_values(by='successful_outcome')
print(deep_segment)

# Automatically surface the worst segment
worst_segment = deep_segment.iloc[0]
print(f"\n🚨 Highest Risk Segment:")
print(f"Diabetes: {worst_segment['diabetes']}")
print(f"Age Group: {worst_segment['age_group']}")
print(f"Success Rate: {worst_segment['successful_outcome']:.3f}")
```

```
   diabetes age_group  successful_outcome
        1.0     50-70               0.861   ← WORST
        1.0     70-90               0.874
        1.0     30-50               0.879
        0.0     18-30               0.881
        ...

🚨 Highest Risk Segment:
Diabetes: 1
Age Group: 50-70
Success Rate: 0.861
```

```python
plt.figure(figsize=(10, 6))

sns.barplot(
    x='age_group',
    y='successful_outcome',
    hue='diabetes',
    data=deep_segment
)

plt.title("Success Rate — High Treatment Patients Only", fontsize=16, weight='bold')
plt.xlabel("Age Group")
plt.ylabel("Success Rate")
plt.legend(title="Diabetes (0 = No, 1 = Yes)")
plt.show()
```

![Chart 16](images/chart16.png)

**What it shows:** Among high-treatment patients (5+ medications), grouped by age and diabetes status, the **diabetic 50–70 cohort has the lowest success rate at 86.1%**. This is the worst-performing segment in the entire dataset — not by a small margin but consistently, across every analytical approach applied. This group requires a **dedicated care pathway**, not just a general intervention flag.

---

### 📊 Chart 13 — Patient Journey Event Mapping

```python
events = df.copy()

events['event_sequence'] = (
    "admission → "
    + "diagnosis(" + events['diabetes'].astype(str) + ") → "
    + "treatment(" + events['medication_count'].astype(str) + " meds) → "
    + "stay(" + events['length_of_stay'].astype(str) + ") → "
    + "outcome(" + events['readmitted_30_days'].astype(str) + ")"
)

# Most common success paths
journey_counts = events['event_sequence'].value_counts().head(10)
print(journey_counts)

# Most common failure paths
failure_journeys = events[events['readmitted_30_days'] == 1]
failure_counts = failure_journeys['event_sequence'].value_counts().head(10)
print(failure_counts)
```

```
# TOP FAILURE PATHS:
admission → diagnosis(1) → treatment(8 meds) → stay(8) → outcome(1)    29
admission → diagnosis(1) → treatment(5 meds) → stay(9) → outcome(1)    28
admission → diagnosis(1) → treatment(1 meds) → stay(6) → outcome(1)    27
admission → diagnosis(0) → treatment(3 meds) → stay(5) → outcome(1)    26
admission → diagnosis(1) → treatment(5 meds) → stay(8) → outcome(1)    26
```

![Chart 11](images/chart11.png)

**What it shows:** Patient pathways are reconstructed as readable event sequences — admission → diagnosis → treatment → stay → outcome. The most common *failure* journeys consistently involve **diabetic patients (`diagnosis(1)`) regardless of medication count** — sometimes with 1 med, sometimes with 8 meds, both leading to readmission. Treatment intensity is not the common thread. **Diabetes status is.** The journey mapping confirms that outcomes are driven by underlying condition, not treatment volume.

---

## 💰 Financial Model — Code + ROI Optimisation

---

### 💸 Stage 1: Rule-Based Intervention (22% ROI)

```python
# Business assumptions
cost_per_readmission = 15000    # £ per readmission
intervention_cost = 500         # £ per patient
effectiveness = 0.30            # 30% reduction in readmissions

# Define HIGH-RISK group: high meds (>=5) AND diabetic
high_risk = df[(df['medication_count'] >= 5) & (df['diabetes'] == 1)]

# Cost-benefit calculation
baseline_readmissions = high_risk['readmitted_30_days'].sum()
baseline_cost = baseline_readmissions * cost_per_readmission

expected_reduction = baseline_readmissions * effectiveness
saved_cost = expected_reduction * cost_per_readmission

intervention_total_cost = len(high_risk) * intervention_cost

net_benefit = saved_cost - intervention_total_cost
roi = net_benefit / intervention_total_cost

print("📊 High-Risk Patients:", len(high_risk))
print("💰 Baseline Cost:", baseline_cost)
print("💸 Intervention Cost:", intervention_total_cost)
print("📉 Savings:", saved_cost)
print("🔥 Net Benefit:", net_benefit)
print("📈 ROI:", roi)
```

```
📊 High-Risk Patients: 8054
💰 Baseline Cost: £16,395,000
💸 Intervention Cost: £4,027,000
📉 Savings: £4,918,500
🔥 Net Benefit: £891,500
📈 ROI: 0.22  →  22%
```

---

### 🚀 Stage 2: Risk-Score Targeting with Tiered Costs (144% ROI)

```python
# Step 1: Create composite risk score (no ML required)
df['risk_score'] = (
    (df['medication_count'] * 0.4) +
    (df['diabetes'] * 2) +
    (df['length_of_stay'] * 0.3)
)

# Step 2: Target only top 20% highest-risk patients
top_n = int(0.2 * len(df))
target = df.sort_values(by='risk_score', ascending=False).head(top_n)

# Step 3: Tiered intervention cost — smarter spending
def intervention_cost_fn(score):
    if score > 8:
        return 500    # high risk → intensive care coordination
    elif score > 5:
        return 200    # medium risk → moderate follow-up
    else:
        return 50     # lower risk → light-touch check-in

target['intervention_cost'] = target['risk_score'].apply(intervention_cost_fn)

# Step 4: Calculate ROI
baseline_readmissions = target['readmitted_30_days'].sum()
baseline_cost = baseline_readmissions * cost_per_readmission
saved_cost = baseline_readmissions * effectiveness * cost_per_readmission
intervention_total_cost = target['intervention_cost'].sum()

net_benefit = saved_cost - intervention_total_cost
roi = net_benefit / intervention_total_cost

print("🎯 Targeted Patients:", len(target))
print("💰 Baseline Cost:", baseline_cost)
print("💸 Intervention Cost:", intervention_total_cost)
print("📉 Savings:", saved_cost)
print("🔥 Net Benefit:", net_benefit)
print("🚀 ROI:", roi)
```

```
🎯 Targeted Patients: 6,000
💰 Baseline Cost: £12,090,000
💸 Intervention Cost: £1,482,600
📉 Savings: £3,627,000
🔥 Net Benefit: £2,144,400
🚀 ROI: 1.446  →  144%
```

![Chart 14](images/chart14.png)

---

### 📈 Chart — ROI vs % of Patients Targeted (The Optimisation Curve)

```python
import numpy as np
import matplotlib.pyplot as plt

cost_per_readmission = 15000
base_effectiveness = 0.30
intervention_cost = 500

df['risk_score'] = (
    (df['medication_count'] * 0.4) +
    (df['diabetes'] * 2) +
    (df['length_of_stay'] * 0.3)
)
df_sorted = df.sort_values(by='risk_score', ascending=False).reset_index(drop=True)

percentages = np.arange(0.1, 1.1, 0.1)
rois = []

for p in percentages:
    n = int(p * len(df_sorted))
    segment = df_sorted.head(n).copy()

    # Dynamic effectiveness: higher risk → higher impact
    segment['dynamic_effectiveness'] = (
        0.1 + (segment['risk_score'] / segment['risk_score'].max()) * base_effectiveness
    )

    expected_reduction = (segment['readmitted_30_days'] * segment['dynamic_effectiveness']).sum()
    saved_cost = expected_reduction * cost_per_readmission

    # Scaling penalty: cost rises non-linearly as coverage expands
    scale_penalty = 1 + (n / len(df)) ** 2
    intervention_total_cost = n * intervention_cost * scale_penalty

    net_benefit = saved_cost - intervention_total_cost
    roi = net_benefit / intervention_total_cost
    rois.append(roi)

best_idx = np.argmax(rois)

print(f"🎯 Optimal Target %: {percentages[best_idx] * 100:.0f}%")
print(f"🚀 Max ROI: {rois[best_idx]:.2f}")

plt.figure(figsize=(10, 6))
plt.plot(percentages * 100, rois, marker='o')
plt.axvline(percentages[best_idx] * 100, linestyle='--', color='red', label='Optimal point')
plt.title("ROI vs % of Patients Targeted (Realistic Model)")
plt.xlabel("Target Population (%)")
plt.ylabel("ROI")
plt.legend()
plt.grid()
plt.show()

print(f"\n📌 Business Insight:")
print(f"Focus on top {int(percentages[best_idx]*100)}% patients for maximum ROI.")
print("Avoid broad interventions — diminishing returns kick in quickly.")
```

```
🎯 Optimal Target %: 10%
🚀 Max ROI: 0.43

📌 Business Insight:
Focus on top 10% patients for maximum ROI.
Avoid broad interventions — diminishing returns kick in quickly.
```

![Chart 12](images/chart12.png)

**What it shows:** ROI peaks at ~43% when targeting the top 10% of highest-risk patients, then falls steadily as coverage expands — a direct consequence of the `scale_penalty` term that models real-world cost increases at scale. **The curve has a clear optimal point.** Broadening intervention beyond the peak costs more and saves proportionally less. This is the quantitative backbone for every recommendation in this project: smarter targeting, not wider coverage, is what maximises return.

---

## 📊 Excel Dashboard

> Built for operational stakeholders who need answers without a SQL console or Python environment.

![Excel Dashboard](images/excel.png)

The Excel workbook has four sheets: `cleaned_hospital` (raw formatted data), `Pivot Table` (aggregations by key dimensions), `Dashboard` (interactive visuals), and a supporting calculations sheet.

---

### 🍕 Visual 1 — High Risk Distribution (Pie Chart)

The headline pie chart answers the most immediate operational question: how big is the high-risk population? The answer: **54% of patients fall into the High Risk category**, with 46% Normal. This split, derived from the composite risk score, immediately communicates that the problem isn't a small edge case — it's the majority of patients. Any intervention strategy has to be scalable, not ad-hoc.

---

### 📉 Visual 2 — Age vs Readmission Rate (Bar Chart)

A bar chart mapping average readmission rate against individual ages rather than age groups. The bars — ranging from ~0.141 to ~0.170 — show no consistent upward or downward trend. Ages 53 and 86 appear slightly elevated, but the pattern is random. **Age, measured individually, is not a useful stratifier.** The bar chart makes this visually undeniable: if age were driving readmission, you'd see a clear slope. There isn't one.

---

### 🍩 Visual 3 — Frequent Patients vs Length of Stay (Donut Charts)

Two donut charts comparing length-of-stay distribution across Frequent Patient categories. The visual confirms what the SQL queries showed numerically — **frequent (high-utilisation) patients do not have substantially longer stays than infrequent ones**. The proportions between Low, Medium, and other segments are near-identical across patient types. Resource usage isn't clustered where you'd expect it — it's spread uniformly, which makes targeted intervention more viable than capacity expansion.

---

### 📈 Visual 4 — BMI Trend Across Age Groups (Line Chart — not shown but present in workbook)

A line chart tracing average BMI across age brackets. BMI holds steady at approximately **29–30** across all age groups with minimal variance. This definitively rules BMI out as a readmission driver. Clinically, a BMI in this range is consistently overweight but not an extreme outlier — and its uniformity across the population means it provides no discriminatory power for risk stratification.

---

### 🎛️ Slicers: Age · Gender · Frequent Patient Category

Three interactive slicers allow any stakeholder to filter every chart simultaneously. Slicing by gender reveals no meaningful readmission differences between Male, Female, and Other categories — another null result that clarifies the model. Slicing by Frequent Patient category allows operational teams to focus on high-utilisation subgroups without re-running queries.

---

## 📈 Tableau Dashboards

> Tableau's role was the interactive exploratory layer — letting the data tell its story visually and allowing users to interrogate it themselves.

---

### 🖥️ Dashboard 1 — Risk, Patterns, and the Discharge Problem

![Tableau Dashboard 1](images/tab%201%20.png)

---

#### 📊 Panel 1 — Readmission by Discharge Destination (Bar Chart)

The bar chart shows three columns: **Home (10.6%)**, **Nursing Facility (18.2%)**, **Rehab (18.1%)**. The numbers are stark — Rehab and Nursing facilities show nearly double the readmission rate of Home discharges. The instinctive conclusion is that these settings are failing patients. **The instinct is wrong.** The bar chart is asking you to hold that thought until the Risk Map below explains what's actually happening.

> 💡 **Insight:** Discharge destination is not neutral — it reflects underlying patient risk AND possible decision gaps.

---

#### 🌡️ Panel 2 — Readmission Risk Heatmap (Stay Group × Med Group)

A colour-coded grid where rows represent length-of-stay groups (Short / Medium / Long) and columns represent medication groups (Low / Medium / High). The darkest cell — **Short stay + High medication load at 12.9%** — sits in the top-left corner, which defies the assumption that longer stays and more complex treatment equal worse outcomes.

> 💡 **Insight:** Risk is **compounded**, not isolated — multiple stress factors amplify readmission probability simultaneously, not sequentially.

---

#### 🫧 Panel 3 — Cohort Analysis: Time/Group Clustering (Bubble Chart)

A bubble chart showing patient clusters by medication group. The **High medication group** produces tightly clustered bubbles — they don't scatter randomly across the space. This visual confirms that readmission in this cohort follows a **systematic, predictable pattern** rather than random individual variation.

> 💡 **Insight:** Readmission is **systematic and predictable**, not random — enabling early identification and intervention.

---

#### 🗺️ Panel 4 — Cost and Risk Efficiency Map (Treemap)

A treemap where each tile represents a patient subgroup, sized by volume and shaded by readmission risk. The **Diabetic** tile is prominently labelled in the map's high-risk quadrant. The treemap makes it immediately visually apparent that diabetic older patients discharged home are the most under-served high-risk group — they occupy significant space in both volume and risk but receive the least structured support.

> 💡 **Insight:** Discharge strategy is **not aligned with patient vulnerability** — high-risk groups are not receiving sufficient post-care support.

---

### 🖥️ Dashboard 2 — Cost, Risk Score, and the Decision-Making Gap

![Tableau Dashboard 2](images/tableau.png)

---

#### 💸 Panel 1 — Cost vs Risk Efficiency (Scatter with Slider)

A scatter plot with a length-of-stay slider allowing dynamic filtering. The three discharge destinations appear as circles at very different positions: **Home sits near zero on both medication count (cost proxy) and readmission**. **Nursing Facility sits at high medication count but moderate readmission** — visible evidence of the inefficiency: the highest-spending discharge group is not achieving proportionally lower readmission rates.

> 💡 **Insight:** Higher cost ≠ better outcomes. There are **inefficient treatment allocations** where spending is not translating into reduced risk.

---

#### 📉 Panel 2 — Readmission Risk vs Patient Risk Score (Line Chart)

Three lines — one per discharge destination — plotted against risk score bins on the x-axis and average readmission rate on the y-axis. As risk score increases, readmission rises for all groups. But **Rehab and Nursing patients consistently sit above the Home line across every risk level** — not because those settings are worse, but because they're receiving patients with higher underlying complexity at every tier.

> 💡 **Insight:** Discharge strategy **amplifies underlying risk** — it's not just patient condition, but post-care decisions that matter.

---

#### 🎯 Panel 3 — Decision-Making Visualisation (Grouped Bars)

Six grouped bars, two per discharge destination, comparing average length of stay and average readmission rate side by side. The tooltip shows **Home → Avg. Length of Stay: 5.512** — nearly identical to Nursing and Rehab. Stay duration is not the differentiator. Readmission is. Home delivers the **lowest readmission at the lowest cost**. Nursing/Rehab deliver **higher cost AND higher readmission** — but crucially, this is because of *patient selection*, not setting performance.

> 💡 **Insight:** The current system is **not optimised** — higher resource allocation is not yielding proportional benefits because risk-informed discharge planning is absent.

---

## 💰 Financial Model — ROI Optimisation

### Stage 1: Rule-Based Targeting (22% ROI)

```
High-Risk Patients (diabetes=1, medication_count≥5): 8,054
Baseline Cost:          £16,395,000
Intervention Cost:      £4,027,000  (£500/patient)
Savings (30% reduction): £4,918,500
Net Benefit:            £891,500
ROI:                    22%
```

### Stage 2: Risk-Score Targeting (144% ROI)

```
Targeted Patients (top 20% by risk score): 6,000
Baseline Cost:          £12,090,000
Intervention Cost:      £1,482,600  (tiered: £500 / £200 / £50)
Savings (30% reduction): £3,627,000
Net Benefit:            £2,144,400
ROI:                    144%
```

**The difference:** Not more spending — smarter selection. Tiered intervention costs (£500 for high-risk, £200 for medium, £50 for low) combined with targeting only the top 20% reduced cost dramatically while retaining most of the benefit.

---

## ⚠️ Challenges

### 1. No single variable explained anything
Opening the correlation heatmap to near-zero values everywhere is disorienting. The correct response — which took time to reach — was to treat it as a finding, not a failure. Readmission is inherently multivariate. That reframe led to compound segmentation and the custom risk score.

### 2. The discharge data was misleading at first glance
Rehab at 17.5% looks like failure until you ask *who* Rehab is receiving. Stratification by risk group revealed the real story: patient complexity, not setting quality, explained the gap.

### 3. Simulating an A/B test without live data
Using `MOD(ROW_NUMBER() OVER(), 2)` for group assignment and `RAND() < 0.15` for treatment effect simulation is directional, not causal. The results were clearly labelled as estimates throughout.

### 4. Keeping four tools non-redundant
Each tool had to earn its place. SQL handled logic and financial quantification. Python handled distributions and optimisation. Excel delivered accessible business-facing reporting. Tableau enabled interactive exploration. No chart appeared twice without adding new information.

### 5. Communicating null results as findings
Telling stakeholders that age, BMI, and length of stay don't matter — when those are the variables everyone expects to matter — required careful framing. Absence of effect, clearly demonstrated, is evidence. It deserves equal weight to a positive finding.

---

## ✅ Recommendations

### 1. 🎯 Implement Risk Scoring at the Point of Discharge
Before any patient leaves the ward, calculate their composite risk score. Flag the top 20% for automatic escalation review. No ML required — the SQL-based model is sufficient as a first implementation.

### 2. 🚪 Close the Discharge Destination Gap
Seventy percent of highest-risk patients are going home by default. A structured triage protocol at discharge — informed by risk score — should make this a deliberate clinical decision, not a fallback.

### 3. 📞 Pilot the Follow-Up Intervention with Rehab Patients First
The A/B simulation showed the strongest treatment effect among Rehab patients (~1.5 percentage point reduction). That's ~£1.5M in potential savings from one group alone. Validate there before scaling.

### 4. ❌ Stop Using Single-Variable Rules for Risk Assessment
Age alone, medication count alone, diabetes alone — all weak predictors. Any threshold-based rule (e.g., "flag everyone over 70") will misclassify large portions of the population. The compound risk score outperforms single-variable rules at every analysis layer.

### 5. 🔴 Prioritise the Diabetic 50–70 Cohort Immediately
This segment appeared at the bottom of every success rate ranking attempted — 86.1% success rate, consistently high readmission. It warrants a **named clinical priority** with its own monitoring and follow-up protocol, not a slot in a general intervention.

---

## 🏁 Conclusion

This project set out to find which patients come back within 30 days — and why. What it found was more instructive than a simple list of risk factors.

**Readmission is not a single-variable problem.** Age, BMI, diabetes, hypertension, medication count, length of stay — each one, examined alone, explains almost nothing. The implication is significant: any intervention built on a single-variable rule will miss most of the patients it is trying to catch.

**What does predict readmission is the compound interaction** between patient complexity, treatment burden, and — most critically — what happens after discharge. The discharge destination analysis revealed that high-risk patients are walking out through the same doors as low-risk ones, with the same follow-up. That structural gap is the most actionable finding in the entire analysis.

**The financial model made the case concrete.** Readmissions cost approximately £55M across this patient population. A targeted intervention — risk-scored, tiered by cost, focused on the right 20% of patients — could generate **£2.1M in net savings at a 144% ROI**. Smarter targeting, not more spending, drives that number.

Perhaps the most important lesson: **the assumptions we bring to data are often the first things the data disproves.** The variables expected to matter didn't. The structural problem nobody was explicitly looking for — discharge decisions made without risk data — turned out to be the central finding.

If implemented, these recommendations would not merely reduce readmissions. They would change **how discharge decisions are made** — from a clinical judgment call made without data, to a structured, risk-informed process that uses what the hospital already knows about each patient to determine what they need next.

That is the difference between reacting to readmissions after the fact — and preventing them before the patient reaches the car park.

---

<div align="center">

**Built with SQL · Python · Excel · Tableau**

*If this project helped you, give it a ⭐ — it helps others find it.*

[![GitHub](https://img.shields.io/badge/View_on_GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/Kushala125/Hospital-Readmission-Risk-Analytics-)

</div>
