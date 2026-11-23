# AQUA — Statistical Analysis and Predictive Modelling of Aquaculture Water Quality

**A research project exploring how data analysis and machine learning can help understand and anticipate water-quality problems in aquaculture ponds.**

---

## 1. Why I Started This Project

My interest in AQUA comes from something I have seen closely in my own family.

My grandfather is an aquaculture farmer. Growing up, I had the opportunity to observe the practical challenges he faced while managing aquaculture ponds. Maintaining suitable pond conditions, identifying potential problems and making timely decisions are important parts of aquaculture farming. These challenges made me curious about whether technology and data analysis could help farmers make better-informed decisions.

One thing became particularly clear to me: **water plays a critical role in aquaculture.**

The condition of pond water directly affects the environment in which fish and prawns grow. Parameters such as dissolved oxygen, pH, temperature, ammonia and other water-quality indicators need to be monitored because changes in these conditions may indicate potential problems.

I started asking myself a few questions:

- What are the important factors that influence aquaculture water quality?
- How do these parameters vary across different ponds and monitoring periods?
- Are there relationships between different water-quality measurements?
- Can historical measurements help identify or anticipate potential water-quality problems?
- Could data analysis eventually help farmers monitor pond conditions more effectively?

I wanted to explore these questions using my interest in programming, statistics and machine learning.

That is how **AQUA** began.

Instead of immediately developing an application or connecting sensors, I decided to understand the data first. I wanted to investigate the measurements, identify patterns, examine inconsistencies and determine whether predictive modelling could provide useful insights.

My long-term goal is to contribute towards practical, data-driven solutions for aquaculture farmers, helping them monitor important water-quality parameters and identify potential problems earlier.

AQUA is my first research-oriented step towards that goal.

---

## 2. Project Objective

The primary objective of AQUA is to investigate whether routine water-quality measurements can help predict the water-quality status recorded during the next observation of an aquaculture pond.

The project follows a research-driven workflow:

1. Understand the available aquaculture dataset.
2. Audit and refine the data before analysis.
3. Investigate water-quality measurements and their relationships.
4. Examine differences across regions, ponds and observation times.
5. Develop predictive models using selected water-quality parameters.
6. Evaluate model performance and investigate prediction errors.
7. Identify the limitations and opportunities for further research.

The project focuses on understanding the evidence before attempting to build a predictive solution.

---

## 3. Dataset

AQUA uses publicly available aquaculture monitoring data released by the **Fish Welfare Initiative (FWI)** through its Alliance for Responsible Aquaculture programme in India.

The dataset contains water-quality observations from aquaculture ponds in:

- Eluru, Andhra Pradesh, India
- Nellore, Andhra Pradesh, India

### Dataset overview

| Description | Details |
|---|---|
| Dataset | FWI Farm Data — India |
| Raw observations | 12,431 |
| Columns | 53 |
| Unique pond identifiers | 317 |
| Eluru observations | 9,248 |
| Nellore observations | 3,183 |
| Observation period | June 2021 – August 2026 |
| Observation types | Morning and Evening |
| Data format | CSV |

### Water-quality parameters examined

The dataset includes measurements and operational information such as:

- Dissolved Oxygen (DO)
- pH
- Water Temperature
- Turbidity
- Total Dissolved Solids (TDS)
- Ammonia measurements
- Water Colour
- Weather
- Feed-related information
- Stocking Density
- Equipment and monitoring information

Not every parameter was measured during every visit. This became one of the important challenges during the data-quality investigation.

**Important:** The project uses the publicly released dataset. It does not use private aquaculture records from my family's farm.

---

## 4. Research Question

The central research question is:

> Can water-quality measurements from an earlier pond observation help predict whether the water quality will be recorded as in range or out of range during the same pond's next observation?

The prediction target is the source dataset field:

`Is WQ in range?`

The target is encoded as:

| Class | Original label | Meaning |
|---|---|---|
| 0 | Yes | Water quality recorded as in range |
| 1 | No | Water quality recorded as out of range |

The project investigates a **next-observation prediction problem**.

This is not a fixed-duration forecast. The next observation may occur after a few hours, several days or a much longer interval.

The model predicts the dataset's recorded water-quality classification. It does not independently diagnose fish health or establish a universal biological safety threshold.

---

## 5. Research Workflow

The project is organised into four Jupyter notebooks.

| Notebook | Research stage | Purpose |
|---|---|---|
| `01_data_quality_audit.ipynb` | Data Quality Audit | Investigating missing values, inconsistencies, unusual measurements, duplicate timestamps and data reliability. |
| `02_exploratory_data_analysis.ipynb` | Exploratory Data Analysis | Understanding measurement distributions, relationships, regional differences and observation patterns. |
| `03_predictive_modelling.ipynb` | Predictive Modelling | Developing and evaluating models to predict the next observation's recorded water-quality status. |
| `04_model_interpretation_error_analysis.ipynb` | Model Interpretation and Error Analysis | Investigating false predictions, missed cases, pond-level errors and the influence of observation intervals. |

The order is intentional. Predictive modelling was performed only after examining the quality and limitations of the underlying data.

---

## 6. Important Findings

### 6.1 Data Quality and Missing Measurements

The initial investigation showed that the availability of measurements varies considerably across parameters.

The three primary modelling measurements had very high availability:

| Parameter | Available values | Missing values | Availability |
|---|---:|---:|---:|
| pH | 12,412 | 19 | 99.85% |
| Dissolved Oxygen | 12,412 | 19 | 99.85% |
| Temperature | 12,410 | 21 | 99.83% |

However, other parameters had considerably more missing observations.

For example:

- Turbidity: 60.60% missing.
- TDS: 63.14% missing.
- Historical TAN-N ammonia: 57.43% missing.
- Newer NH3 measurement: 86.65% missing.
- Feed amount: 45.65% missing.

This demonstrated that using every available parameter in a predictive model would not necessarily produce a reliable result.

Missing measurements also required careful interpretation because some parameters were collected conditionally rather than during every monitoring visit.

### 6.2 Morning and Evening Differences

One of the clearest patterns emerged when comparing morning and evening measurements.

| Parameter | Morning median | Evening median |
|---|---:|---:|
| Dissolved Oxygen | 3.5 mg/L | 10.2 mg/L |
| pH | 8.11 | 8.38 |
| Temperature | 28.8°C | 30.8°C |

Dissolved oxygen showed a particularly noticeable difference between morning and evening observations.

This reinforced the importance of considering observation time when analysing aquaculture water-quality measurements.

A pooled analysis without observation-time context could hide important differences in the data.

### 6.3 Relationships Between Water-Quality Parameters

The exploratory analysis identified positive correlations between the three primary measurements.

| Parameter relationship | Pearson correlation |
|---|---:|
| Dissolved Oxygen and pH | +0.490 |
| Dissolved Oxygen and Temperature | +0.416 |
| pH and Temperature | +0.331 |

These relationships are descriptive associations. They do not establish that one parameter causes changes in another.

The relationship between dissolved oxygen and pH also differed between morning and evening observations, showing why observation conditions and regional context matter.

### 6.4 Regional and Monitoring Differences

The dataset contains observations from Eluru and Nellore, but the monitoring histories are not identical.

Monitoring frequency, observation intervals and measurement practices vary between ponds and regions.

The analysis therefore treats the two regions separately during predictive modelling rather than assuming that a single model will behave equally well across both datasets.

### 6.5 Predictive Modelling Results

The project compared two classification algorithms:

- Logistic Regression
- Random Forest

The primary model predictors were:

- pH
- Dissolved Oxygen
- Temperature

Models were evaluated separately for Eluru and Nellore using pond-aware chronological evaluation.

The positive class represents water quality recorded as out of range.

#### Test-set performance

| Region | Model | Accuracy | Precision | Recall | F1-score | ROC-AUC |
|---|---|---:|---:|---:|---:|---:|
| Eluru | Logistic Regression | 55.91% | 18.12% | 61.22% | 0.2796 | 0.6501 |
| Eluru | Random Forest | 77.60% | 31.71% | 52.24% | 0.3947 | 0.7456 |
| Nellore | Logistic Regression | 93.54% | 62.96% | 38.64% | 0.4789 | 0.6313 |
| Nellore | Random Forest | 92.32% | 50.00% | 47.73% | 0.4884 | 0.7731 |

These results show why accuracy alone is insufficient when evaluating an imbalanced classification problem.

An earlier majority-class baseline achieved 86.02% accuracy while detecting none of the out-of-range cases.

The Random Forest model achieved a stronger overall balance of metrics in the current evaluation, particularly in Eluru. However, Logistic Regression achieved higher recall in Eluru at its selected threshold.

In Nellore, most paired metric differences between the two models remain uncertain, although Random Forest achieved a higher ROC-AUC.

The results support retaining Random Forest as the preferred research candidate, not declaring it universally superior.

### 6.6 Prediction Errors and Observation Intervals

The final notebook investigated where the models made incorrect predictions.

The analysis examined:

- False positives.
- False negatives.
- Errors across individual ponds.
- Differences between morning and later-day input observations.
- The time elapsed between consecutive observations.
- Model performance across short, medium and long observation intervals.

An important finding was that prediction performance varied with the elapsed time between observations.

For example, in Eluru, Random Forest recall was:

| Observation interval | Recall |
|---|---:|
| Short (up to 24 hours) | 68.45% |
| Medium (over 24 hours to 7 days) | 53.49% |
| Long (over 7 days) | 24.75% |

These results indicate that prediction quality can differ substantially depending on the observation interval.

However, interval length, observation time, region and sample composition are intertwined. The analysis does not establish that longer gaps alone cause lower predictive performance.

---

## 7. Major Challenges Faced During Development

One of the most important lessons from building AQUA was that **data refinement was more challenging than developing the machine-learning models themselves.**

The dataset contained thousands of observations, but the presence of a large number of rows did not automatically mean that every observation was ready for analysis.

Before building models, I had to understand how the data was collected, what the columns represented, which measurements were reliable and which records required additional investigation.

### 7.1 Refining and Understanding the Dataset

This was the biggest challenge.

The dataset contained 53 columns, different measurement protocols, missing observations, unusual values and inconsistencies.

Rather than immediately removing incomplete rows or replacing missing values, I investigated the reason behind the missingness and whether a measurement was actually collected during that visit.

This was important because automatically deleting incomplete observations could have removed a substantial amount of useful information.

### 7.2 Missing Values Were Not Always Data Errors

Some parameters were collected only during particular types of visits.

For example, turbidity and ammonia measurements had substantially higher missingness than pH, dissolved oxygen and temperature.

I had to distinguish between:

- A measurement that was expected but not recorded.
- A parameter that was not collected during that visit.
- A parameter that was introduced or changed under a newer monitoring protocol.

This influenced which variables could reasonably be used in the modelling stage.

### 7.3 Different Ammonia Measurement Protocols

The dataset contained three ammonia-related fields:

- Historical TAN-N.
- Transitional TAN (NH3).
- New NH3.

These measurements had different collection periods and definitions.

They could not simply be combined into one continuous ammonia series.

I investigated their availability, overlapping records, thresholds and formula consistency. The investigation also showed why ammonia should not automatically be included as a predictor without first resolving differences in measurement protocols.

### 7.4 Unusual Values and Outliers

Some measurements contained extreme values.

For example, TDS included a maximum recorded value of 936.00, with 242 observations flagged using the interquartile-range method.

Stocking density also contained unusually high and repeated values.

These observations were investigated rather than automatically deleted.

An outlier is not necessarily an incorrect measurement. Removing it without understanding its origin could introduce another form of data-quality error.

### 7.5 Duplicate Timestamps and Uneven Monitoring Intervals

The dataset contained repeated pond-date-time groups with different morning/evening labels.

These records created ambiguity when arranging observations chronologically.

Because AQUA predicts the next observation for the same pond, incorrect chronological ordering could affect the target construction.

The modelling workflow therefore excludes transitions touching ambiguous duplicate timestamps rather than arbitrarily selecting an order.

Monitoring intervals also varied substantially between ponds.

Some consecutive observations were separated by hours, while others were separated by days or months.

This made it necessary to examine the elapsed time between observations rather than treating every prediction as if it represented the same forecasting period.

### 7.6 Data Retraction and Measurement Changes

Another important challenge came from changes documented by the original data provider.

In October 2026, the Fish Welfare Initiative reported data-integrity concerns and retracted all Nellore water-quality observations collected on or after 1 November 2024.

AQUA uses the updated main dataset and does not reintroduce those retracted records.

Measurement methods and monitoring ranges also changed during the study period.

These changes meant that historical observations could not automatically be assumed to be directly comparable across every region and year.

### 7.7 Preventing Target Leakage

During the predictor-selection process, I identified fields that directly described the recorded outcome or actions taken after a problem was identified.

For example, `Parameters out of range` directly describes the water-quality classification.

Including such information as an independent predictor would allow a model to use information that would not genuinely be available before the outcome.

These fields were excluded from the baseline prediction features.

### 7.8 Class Imbalance and Model Evaluation

Most observations in the dataset were labelled as water quality in range.

This created an imbalanced classification problem.

A model could achieve high accuracy simply by predicting the majority class while failing to identify actual out-of-range observations.

I therefore evaluated models using precision, recall, F1-score, balanced accuracy, average precision and ROC-AUC in addition to accuracy.

The project also used pond-level bootstrap comparisons to investigate uncertainty in the differences between models.

**The biggest lesson:** A predictive model is only as meaningful as the data, target definition and evaluation strategy behind it.

---

## 8. Data Integrity and Responsible Use

The project uses the updated main release of the FWI dataset.

The Nellore observations collected on or after 1 November 2024 were retracted by the source organisation and are not reintroduced into AQUA.

The project also distinguishes between the source dataset's licence and the licence of the project's own code and analysis.

The source dataset is released under the Creative Commons Attribution 4.0 International licence (CC BY 4.0). This does not automatically assign the same licence to the AQUA notebooks or code.

The original source documentation should be consulted before reusing the dataset, particularly for current data corrections, privacy notes and measurement definitions.

---

## 9. Technologies Used

| Category | Tools |
|---|---|
| Programming Language | Python |
| Data Manipulation | Pandas, NumPy |
| Data Visualisation | Matplotlib, Seaborn |
| Statistical Analysis | SciPy, Statsmodels |
| Machine Learning | Scikit-learn |
| Model Persistence | Joblib |
| Development Environment | JupyterLab, VS Code |
| Version Control | Git and GitHub |

---

## 10. Repository Structure

```text
AQUA-DS-2026/
│
├── data/
│   └── raw/
│       └── water_quality.csv
│
├── docs/
│   └── source_reference/
│       ├── data_dictionary.md
│       └── official_README.md
│
├── notebooks/
│   ├── 01_data_quality_audit.ipynb
│   ├── 02_exploratory_data_analysis.ipynb
│   ├── 03_predictive_modelling.ipynb
│   └── 04_model_interpretation_error_analysis.ipynb
│
├── outputs/
│   ├── figures/
│   ├── model_artifacts/
│   └── tables/
│
├── .gitignore
└── README.md
```

The `outputs` directory contains the exported research figures, audit tables, model comparisons, prediction records and fitted model artifacts.

---

## 11. Limitations

AQUA is a retrospective research project based on publicly available observational data.

The current models should not be treated as a deployed farm-monitoring system or an automatic decision-making tool.

Important limitations include:

- The prediction target is the next recorded observation, not a fixed-duration forecast.
- Monitoring frequency differs across ponds.
- The recorded water-quality classification may reflect changing monitoring criteria and practices.
- Some measurements have substantial missingness.
- Ammonia measurement protocols changed over time.
- Regional datasets have different historical coverage and measurement conditions.
- Model performance on historical observations does not guarantee performance on new farms or future observations.
- The practical costs of missed problems and false alerts have not yet been established through collaboration with farm operators.
- The models have not undergone prospective validation using newly collected farm data.

These limitations are important when interpreting the results.

---

## 12. Future Direction

AQUA started with a simple personal motivation: I wanted to understand whether technology could help address some of the challenges I had seen my grandfather face in aquaculture farming.

The research has helped me understand that solving a practical problem requires more than developing an application or training a machine-learning model.

It requires understanding the domain, investigating the data, identifying measurement limitations, evaluating predictions and working with the people who actually face the problem.

My future direction for AQUA is to move progressively towards practical aquaculture support.

Some areas I would like to explore include:

1. **Improved Water-Quality Monitoring:** Investigating ways to track important parameters such as dissolved oxygen, pH, temperature and ammonia more consistently.

2. **Sensor Integration:** Exploring the possibility of collecting measurements through suitable water-quality sensors rather than depending entirely on manual entries.

3. **Predictive Analysis:** Improving the reliability of models through additional data, independent validation and better understanding of observation intervals.

4. **Early Identification of Potential Problems:** Investigating whether reliable predictions can help farmers identify conditions that deserve attention.

5. **Farmer-Friendly Technology:** Exploring simple interfaces that communicate important information clearly, including support for regional languages.

6. **Practical Validation:** Working with aquaculture farmers and domain experts to understand whether the research findings are useful in real farming conditions.

The ultimate objective is not simply to achieve a high machine-learning score.

It is to investigate whether data and technology can help aquaculture farmers understand their pond conditions better and make more informed decisions.

---

## 13. Running the Project

### Clone the repository

```bash
git clone https://github.com/rohithg-codes/AQUA-DS-2026.git
cd AQUA-DS-2026
```

### Create a Python environment

**Windows PowerShell:**

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

### Install the required packages

```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn statsmodels joblib jupyterlab ipykernel
```

### Start JupyterLab

```bash
cd notebooks
jupyter lab
```

Run the notebooks in the following order:

1. `01_data_quality_audit.ipynb`
2. `02_exploratory_data_analysis.ipynb`
3. `03_predictive_modelling.ipynb`
4. `04_model_interpretation_error_analysis.ipynb`

The notebooks use relative paths to access the data and output directories. Launching JupyterLab from the `notebooks` directory preserves the expected paths.

The notebooks were developed using Python 3.13.16. Package versions are not currently pinned in a dependency lockfile, so a fresh environment may require additional compatibility checks.

---

## 14. Data Source and Acknowledgement

The dataset used in this project was released by:

**Fish Welfare Initiative — FWI Farm Data, India**

The source documentation, data dictionary, known data issues and licensing information should be consulted when using or interpreting the dataset.

The dataset is used under its published CC BY 4.0 terms. Appropriate attribution to the original data provider is required.

---

## 15. Author

**GOTTAM ROHITH**

A personal research project driven by an interest in programming, statistical analysis, machine learning and the application of technology to practical aquaculture challenges.

---

*From observing aquaculture challenges in my own family to investigating water-quality data through statistical analysis and machine learning — AQUA is a step towards making technology more useful for aquaculture farmers.*
