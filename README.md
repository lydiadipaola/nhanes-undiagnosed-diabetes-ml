# nhanes-undiagnosed-diabetes-ml
Machine learning analysis of undiagnosed diabetes risk using CDC NHANES 2017–March 2020 data.
# Predicting Undiagnosed Diabetes Using Machine Learning

## Overview

This project uses CDC National Health and Nutrition Examination Survey (NHANES) data to develop machine learning models for identifying adults with laboratory-defined elevated HbA1c who reported no prior diagnosis of diabetes.

The project focuses on whether routinely collected demographic, socioeconomic, behavioral, and anthropometric characteristics can identify individuals who may benefit from further diabetes screening.

**Research question:**
Can non-laboratory characteristics predict elevated HbA1c consistent with diabetes among adults without a self-reported diabetes diagnosis?

---

## Dataset

**Source:** CDC National Health and Nutrition Examination Survey (NHANES)

**Data:** 2017–March 2020 pre-pandemic combined dataset

The analysis combines information from:

* Demographics
* Body measurements
* Diabetes questionnaire
* Smoking questionnaire
* Physical activity questionnaire
* Glycohemoglobin laboratory measurements

The NHANES data are publicly available from the CDC.

---

## Outcome

The target outcome was defined as:

**HbA1c ≥ 6.5% AND no self-reported prior diabetes diagnosis**

HbA1c was used **only to define the outcome** and was not included as a predictor, preventing target leakage.

This outcome represents laboratory-defined elevated HbA1c consistent with diabetes rather than a confirmed clinical diagnosis.

### Outcome distribution

* Total adults analyzed: **8,464**
* Outcome-positive participants: **228**
* Outcome prevalence: **2.69%**

The relatively low prevalence makes class imbalance an important consideration in model evaluation.

---

## Predictors

The models used variables that could be available without laboratory testing:

| Category          | Predictors                         |
| ----------------- | ---------------------------------- |
| Demographics      | Age, sex, race/ethnicity           |
| Socioeconomic     | Education, income-to-poverty ratio |
| Anthropometric    | BMI, waist circumference           |
| Behavioral        | Smoking history                    |
| Physical activity | Vigorous and moderate activity     |

Missing values were handled within the modeling pipeline using median imputation for numeric variables and most-frequent imputation for categorical variables.

Categorical predictors were one-hot encoded.

---

## Machine Learning Methods

Three supervised learning models were evaluated:

1. **Logistic Regression**
2. **Random Forest**
3. **XGBoost**

The dataset was divided into training and testing sets using an **80/20 stratified split**.

Class imbalance was addressed during model training using class weighting for Logistic Regression and Random Forest and a positive-class weight for XGBoost.

---

## Model Performance

| Model                   |   ROC-AUC |    PR-AUC | Brier Score |
| ----------------------- | --------: | --------: | ----------: |
| **Logistic Regression** | **0.742** | **0.069** |       0.201 |
| XGBoost                 |     0.718 |     0.063 |       0.132 |
| Random Forest           |     0.694 |     0.054 |       0.044 |

Logistic Regression achieved the highest ROC-AUC and PR-AUC and was therefore selected as the primary predictive model.

Because the models used class weighting, the initial Brier scores were not interpreted as calibrated probability estimates.

---

## Probability Calibration

The selected Logistic Regression model was subsequently calibrated using **sigmoid calibration with 5-fold cross-validation**.

### Calibrated model performance

* **ROC-AUC:** 0.742
* **PR-AUC:** 0.069
* **Brier Score:** 0.026

Calibration had minimal effect on discrimination while substantially improving the reliability of predicted probabilities.

---

## Model Interpretation

Logistic Regression coefficients were examined to understand which predictors had the greatest influence on model predictions.

The strongest influences included:

* Age
* Waist circumference
* BMI
* Physical activity
* Smoking history
* Education
* Race/ethnicity
* Sex

Age and waist circumference were among the strongest predictors in the model.

These coefficients are presented as **model influences rather than causal effects**. The analysis does not establish that any predictor causes diabetes.

---

## Key Findings

* Logistic Regression provided the strongest discrimination among the three models tested.
* The model achieved an **ROC-AUC of 0.742**, indicating moderate ability to distinguish participants with and without the target outcome.
* The **PR-AUC of 0.069** was above the approximate outcome prevalence of 2.69%, which is particularly relevant given the rare outcome.
* Probability calibration substantially improved the Brier score from **0.201 to 0.026**.
* Anthropometric characteristics, particularly **age and waist circumference**, were among the most influential predictors.

---

## Limitations

Several limitations should be considered:

* The analysis uses a cross-sectional survey, so temporal or causal relationships cannot be established.
* The outcome is based on a single HbA1c measurement combined with self-reported diabetes history.
* The relatively small number of outcome-positive participants limits precision.
* Some questionnaire variables contain sparse response categories.
* NHANES has a complex survey design; this analysis does not use survey weights for the machine learning model.
* Model performance has not been externally validated in an independent population.
* The model should not be interpreted as a clinical diagnostic tool.

---

## Reproducibility

The notebook downloads the NHANES data directly from the CDC rather than requiring the raw datasets to be stored in the repository.

### Tools

* Python
* pandas
* NumPy
* scikit-learn
* XGBoost
* Matplotlib

### Notebook

**`NHANES_Undiagnosed_Diabetes_ML.ipynb`**

---

## Project Structure

```text
nhanes-undiagnosed-diabetes-ml/
│
├── NHANES_Undiagnosed_Diabetes_ML.ipynb
├── README.md
└── figures/
```

---

## Disclaimer

This project is intended for educational and portfolio purposes. It is not a clinical prediction model and should not be used to diagnose or make treatment decisions for individual patients.
