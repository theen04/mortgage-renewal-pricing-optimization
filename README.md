# Mortgage Renewal Pricing Optimization Using Machine Learning

## Project Overview

This project demonstrates an end-to-end machine learning workflow for mortgage renewal pricing optimization. The objective is to simulate customer-level mortgage renewal decisions, predict offer acceptance probability, and identify pricing strategies that balance customer retention and profitability.

The project explores how customer attributes, behavioral characteristics, and pricing sensitivity can be combined to support analytically driven mortgage pricing decisions.

Because proprietary banking renewal data is not publicly available, this project uses a synthetic dataset designed to replicate realistic customer, pricing, and behavioral patterns while preserving full reproducibility.

---

## Business Problem

Mortgage renewal pricing requires balancing competing objectives:

- Maintain strong customer retention
- Avoid unnecessary rate discounts
- Understand customer sensitivity to pricing changes
- Optimize revenue while preserving customer relationships

A successful pricing strategy requires more than predicting whether a customer will accept an offer. It requires estimating acceptance probability and using those predictions to identify profitable offer strategies.

---

## Project Workflow

The notebook follows a complete modeling and optimization pipeline:

### 1. Synthetic Data Generation

A customer-level mortgage dataset is created to simulate realistic banking data.

Features include:

- FICO score
- Income
- Debt-to-income ratio
- Mortgage term
- Customer tenure
- Funding rates
- Offered rates
- Rate elasticity
- Loyalty factors
- Market sensitivity
- Behavioral customer segments

Synthetic generation allows controlled experimentation while maintaining realistic variability.

---

### 2. Exploratory Data Analysis and Assumption Validation

The dataset is analyzed to understand:

- Customer characteristics
- Segment behavior
- Pricing distributions
- Acceptance probability patterns
- Feature relationships

Behavioral assumptions are explicitly examined, including:

- Customer loyalty effects
- Rate sensitivity differences
- Segment-level pricing behavior
- Relationship between mortgage spreads and acceptance probability

---

### 3. Acceptance Probability Modeling

Multiple classification approaches are evaluated to estimate customer acceptance probability:

- Logistic Regression
- Random Forest
- XGBoost

Model evaluation focuses on:

- ROC-AUC performance
- Probability calibration
- Interpretability
- Suitability for pricing optimization

Calibrated probabilities are used because pricing decisions depend on expected outcomes rather than simple classification labels.

---

### 4. Mortgage Rate Optimization

Predicted acceptance probabilities are incorporated into a customer-level pricing optimization framework.

The optimization process evaluates:

- Offered rate scenarios
- Probability of acceptance
- Expected profitability
- Segment-specific customer behavior

The goal is to identify pricing decisions that maximize expected value while maintaining customer relationships.

---

### 5. Results and Insights

The final analysis compares baseline and optimized pricing strategies.

Key outputs include:

- Acceptance probability analysis
- Customer response curves
- Model performance comparisons
- Segment-level optimization results
- Expected profit improvement analysis

---

## Production Considerations

This project demonstrates the modeling workflow required for an analytical pricing solution. In a production mortgage pricing environment, additional capabilities would be required, including:

- Model versioning and artifact management
- Automated data pipelines
- Feature store implementation
- Model monitoring and drift detection
- Governance and validation processes
- Deployment infrastructure

The notebook focuses on demonstrating the analytical framework and decision-support approach.

---

## Repository Structure

```
mortgage-pricing-model/
│
├── mortgage_pricing_model.ipynb
├── README.md
├── requirements.txt
│
└── outputs/
├── figures/
└── metrics/
```


---

## Technologies Used

- Python
- Pandas
- NumPy
- Scikit-learn
- XGBoost
- Matplotlib
- Seaborn
- Jupyter Notebook

---

## How to Run

Install dependencies:

```bash
pip install -r requirements.txt
```

Open the notebook:

```bash
jupyter notebook mortgage_pricing_model.ipynb
```