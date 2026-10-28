# Mortgage Renewal Pricing Optimization Using Machine Learning

## Project Overview

This project demonstrates an end-to-end machine learning workflow for mortgage renewal pricing optimization. It simulates customer-level renewal decisions, predicts offer acceptance probability, and recommends customer-specific renewal rates that balance customer retention and profitability.

Because proprietary banking renewal data is not publicly available, the project uses a synthetic dataset designed to replicate realistic customer, pricing, and behavioral patterns. A synthetic setting has one major advantage: the true customer behavior is known, so every pricing recommendation can be scored against ground truth and compared with the best achievable outcome.

---

## Business Problem

Mortgage renewal pricing requires balancing competing objectives:

- Maintain strong customer retention
- Avoid unnecessary rate discounts
- Understand how sensitivity to price differs across customers
- Optimize margin while preserving long-standing customer relationships

Predicting whether a customer will accept an offer is not enough. A pricing model must also estimate how acceptance *changes* as the price changes, and those estimates must be turned into decisions that respect business rules.

---

## Key Results

Optimized offers for 6,000 held-out customers in the final month, scored with the true acceptance function:

| Strategy | Avg rate change | Acceptance | Annual margin uplift | Share of best achievable uplift |
|---|---|---|---|---|
| Current offers | — | 63.4% | — | — |
| **Logistic Regression (selected)** | −9 bps | 70.7% | **+$1.79M (+9.1%)** | **87%** |
| XGBoost | −6 bps | 69.5% | +$1.72M | 83% |
| Random Forest | +11 bps | 61.2% | +$0.73M | 35% |
| Oracle (true behavior) | −7 bps | 70.7% | +$2.07M | 100% |

- **Pricing is differentiated by segment.** Price-sensitive shoppers average −27 bps and subprime customers −42 bps, while less sensitive premium customers average +18 bps.
- **Margin and retention improve together.** Discounts are concentrated where they change behavior most, so acceptance rises from 63% to 71% while margin grows 9%.
- **The model captures most of the achievable value.** Recommendations correlate at 0.91 with the oracle's, and capture 87% of its uplift.
- **AUC alone is a poor guide for pricing models.** Random Forest's AUC is within 0.005 of Logistic Regression's, but it understates price sensitivity, raises rates where it should discount, and captures only about a third of the achievable value.

---

## Project Workflow

### 1. Synthetic Data Generation

A 36-month panel of 20,000 customers (720,000 offer records) is generated with:

- FICO score (U.S. population distribution), log-normal income, and debt-to-income ratio
- Mortgage term, tenure, and loan amount
- Term-based funding rates and historical offers that vary around a target spread, providing the price variation needed to learn rate sensitivity
- Rate elasticity, market sensitivity, and loyalty that increases with tenure
- One behavioral segment per customer (shopper, loyal, risk-averse, premium, subprime), conditioned on the customer's profile

### 2. Exploratory Data Analysis

Distributions and relationships are checked against the generation assumptions, including FICO against population targets, segment composition, offered spreads by term, and stability of rates over time.

### 3. Acceptance Probability Modeling

A behavioral acceptance function defines the true probability of acceptance, with segment-specific price sensitivity and a steeper penalty for clearly uncompetitive offers. Three models are trained on observable features only:

- **Logistic Regression** with spread × segment interactions, so each segment can have its own price slope
- **Random Forest**
- **XGBoost** with a monotone constraint, so predicted acceptance can only fall as rates rise

Models are evaluated on ROC-AUC, Brier score, log loss, calibration, and **price response fidelity**: how closely each model's predicted response to rate changes matches the true response, by segment.

### 4. Mortgage Rate Optimization

For every customer, the optimizer evaluates rate changes from −75 to +50 bps and selects the one that maximizes expected annual margin:

```
expected annual margin = spread over funding × loan amount × P(accept)
```

Business rules are enforced on every recommendation:

- **Spread floor:** offers stay at least 75 bps above funding
- **Loyalty protection:** loyal customers and those with 10+ years of tenure receive at most a +15 bps increase

### 5. Evaluation and Insights

Current, model-optimized, and oracle pricing strategies are compared on true outcomes at the portfolio and segment level, including the share of the best achievable uplift each model captures and how often recommendations are bound by the search range or business rules.

---

## Limitations

- **The acceptance behavior is synthetic.** Results demonstrate the method on known ground truth rather than measuring real customer behavior.
- **The objective is simplified.** It uses one year of margin on full principal. A production version would use lifetime value, amortizing balances, and the cost of replacing a lost customer.
- **Real pricing data is confounded.** Here, historical offers vary randomly. In practice, rate desks discount customers they believe are at risk, so estimating true price sensitivity requires pricing experiments or causal adjustment.

---

## Production Considerations

In a production mortgage pricing environment, additional capabilities would be required, including:

- Model versioning and artifact management
- Automated data pipelines and a feature store
- Model monitoring and drift detection, including monitoring of price sensitivity
- Governance, validation, and human approval of pricing changes
- Deployment infrastructure

---

## Repository Structure

```
mortgage_renewal_pricing_optimization/
│
├── mortgage_pricing_model.ipynb
├── README.md
├── requirements.txt
│
└── outputs/
    ├── figures/
    ├── metrics/
    ├── reproducibility/
    ├── models/          # generated, not committed
    └── data/            # generated, not committed
```

---

## Technologies Used

- Python 3.11
- Pandas, NumPy, SciPy
- Scikit-learn
- XGBoost
- Matplotlib, Seaborn
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

All randomness is driven by a single seeded generator (`SEED = 42`), so results are fully reproducible. The full notebook runs in about 30 seconds and writes figures, metrics, models, and data to `outputs/`.
