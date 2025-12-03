# Home Credit Default Risk - Final Project Description

This repository presents my end-to-end solution for the Kaggle competition [Home Credit Default Risk](https://www.kaggle.com/competitions/home-credit-default-risk/overview), implemented as the final project for my university Big Data course. The goal of the competition is to predict the likelihood that a loan applicant will default on their repayment.

The dataset consists of the applicant’s current loan application along with multiple auxiliary tables that capture their historical credit behavior, previous loan applications, credit bureau records, repayment history, and monthly balance information.

The performance metric used for evaluation is AUC.

## Project Structure

The repository is organized into:

- **homecredit_data_and_fe_descriptions.xlsx**  
  Contains data descriptions and detailed notes on feature engineering steps.
- **eda/**  
  Exploratory Data Analysis notebooks for each dataset, used to inspect distributions, identify anomalies, and inform feature engineering decisions.
- **fe/**  
  Feature engineering notebooks covering all tables in the dataset, producing the processed features used for model training.
- **models/**  
  Notebooks for training the final models, including multiple LightGBM variations and a Random Forest baseline.
- **sub_files/**  
  Stores submission files generated during model evaluation and experimentation.

## Feature Engineering Overview

The feature engineering process includes three main groups. A detailed explanation of all engineered features is provided in **homecredit_data_and_fe_descriptions.xlsx.**

### 1. Features from the Main Application Table

These features summarize each applicant’s financial capacity, stability, and household profile.
They include:
- Credit-to-income ratios
- Payment burden indicators
- Employment and age-related stability metrics
- Family and housing features
- One-hot Encoding for other categorical variables.

## 2. Aggregation Features from Auxiliary Tables
_(Bureau, Previous Application, Installments, POS Cash, Credit Card)_

These auxiliary tables contain multiple historical records per customer. To transform them into features, we:
- Grouped records by SK_ID_CURR
- Computed descriptive statistics across historical entries (count, mean, max, min, sum, std)
- Created separate aggregations by loan status (active, closed, approved, refused)
- Generated delinquency and utilization indicators

### 3. Time-based, Behavior-based, and Ratio Features (4 Monthly Tables)
_(POS_CASH_balance, Credit_Card_balance, Previous_Application, Installments_Payments)_

We applied a consistent feature engineering strategy:
- Group monthly or historical records by SK_ID_PREV, then aggregate them to the customer level using SK_ID_CURR
- Extract recent behavioral patterns using statistics from the latest observations (last, max, min, mean, sum)
- Compute repayment-quality indicators such as overdue counts, days past due, and mismatches between scheduled vs. actual payments
- Engineer spending and utilization ratios (balance ratios, drawing/limit ratios, payment-to-minimum-payment ratios, repayment completeness, ...)
- One-hot Encoding for other categorical variables.
- Create features to capture loan lifecycle timing such as decision dates, due dates, and installment schedules

## Models & Performance

We trained three single models and evaluated them using **Public LB**, **Private LB**, and **local cross-validation**:

| Model | Private LB | Public LB | Local CV |
|-------|------------|------------|-----------|
| **LightGBM 1** | 0.7957 | 0.7981 | 0.7957 |
| **LightGBM 2 (reduced features)** | 0.7953 | 0.7968 | 0.7952 |
| **Random Forest** | 0.7852 | 0.7861 | 0.7830 |

The two LightGBM models form the core of the final solution, delivering the strongest and most stable performance. The Random Forest model acts as a straightforward but valuable baseline, contributing additional model diversity during experimentation.

Below are the key evaluation plots of the selected LightGBM model, including  
(1) 5-fold CV ROC curves
(2) Top feature importances


### ROC Curve 
<img width="590" height="590" alt="image" src="https://github.com/user-attachments/assets/d05ff809-3104-401f-80ea-17b58fd8d93a" />

### Feature Importances (TOP 50)
<img width="1991" height="1389" alt="image" src="https://github.com/user-attachments/assets/6c20f84b-4e4e-4f12-89fe-77bbeee3a4f5" />
