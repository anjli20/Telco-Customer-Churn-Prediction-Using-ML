## Telco Customer Churn Prediction Using ML

Every month telecom companies lose customers to competitors. The cost of acquiring a replacement customer is 5 to 7 times higher than retaining an existing one. The challenge is not knowing **who** will leave **before** they actually leave.

This project solves that by building a system that:
- Predicts churn probability for every customer
- Explains the specific reasons behind each prediction
- Groups customers into actionable risk tiers
- Recommends the right retention offer for each group
- Quantifies the financial impact of the entire approach

The result is not just a model — it is a complete **churn intelligence system** that a real business can act on immediately.


##  Dataset

| Detail | Information |
|--------|-------------|
| File | `WA_Fn-UseC_-Telco-Customer-Churn.csv` |
| Source | IBM Sample Dataset (Kaggle) |
| Records | 7,032 (after cleaning) |
| Features | 20 original + 6 engineered = 26 total |
| Target | Churn (1 = Left, 0 = Stayed) |
| Class Split | 73.4% No Churn / 26.6% Churn |

The dataset covers customer demographics, subscribed services, account details and billing information for a fictional US telecom company.


##  What Makes This Project Different

Most churn prediction tutorials stop at training a model and checking accuracy. This project goes several steps further:

**Beyond basic modeling:**
- Class imbalance fixed with SMOTE (not just ignored)
- 6 custom engineered features derived from domain knowledge
- Two optimized thresholds — one for individuals, one for bulk campaigns
- SHAP values explain every prediction in human-readable terms
- Learning curves diagnose whether each model is overfitting

**Beyond metrics:**
- Business cost analysis translates model performance into dollars
- A risk scoring system (0-100) replaces binary Yes/No predictions
- Personalized retention strategies assigned per risk tier
- Full production pipeline with no data leakage


## Results at a Glance

### Best Model Performance

| Metric | Value | What It Means |
|--------|-------|---------------|
| AUC Score | **0.8397** | Strong class separation |
| Recall | **0.7859** | Catches 78.6% of real churners |
| F1 Score | **0.6194** | Good balance precision/recall |
| Best Model | **Logistic Regression** | Simple but generalizes best |

### Why Logistic Regression Beat Everything

```
Random Forest CV F1 = 0.8598 (looked best in CV)
Random Forest gap   = 0.1482 → overfitting on training data

Logistic Regression CV F1 = 0.7880 (looked worse)
Logistic Regression gap   = 0.0012 → perfect generalization

On real test data, LR outperformed RF on AUC.
A model that generalizes is always more valuable
than one that memorizes.
```

### Business Value

```
Do Nothing:          $0
Random Outreach:    -$8,990  (negative value)
ML Model (bulk):   $74,080
vs Random:         +$83,070 improvement
```

##  Key Findings

### Top Churn Predictors (from SHAP)

| Rank | Feature | SHAP Value | Business Meaning |
|------|---------|------------|-----------------|
| 1 | **tenure** | 0.0516 | New customers churn 47.7% vs long-term 11.9% |
| 2 | **Fiber Optic** | 0.0510 | Fiber users churn more — pricing or quality issue |
| 3 | **Two Year Contract** | 0.0503 | Long contracts strongly reduce churn |
| 4 | **Electronic Check** | 0.0420 | Payment method signals commitment level |
| 5 | **tenure_group_Short** | 0.0311 | Engineered feature — first 12 months are critical |
| 6 | **new_high_charges** | 0.0180 | New + expensive = 69.8% churn rate |

### Engineered Feature Impact

6 new features created from domain knowledge. Three of them rank in the top 15 SHAP values proving that feature engineering added genuine predictive power. Overall AUC improved by +0.0018 after engineering.

### Class Imbalance Impact

Without SMOTE the model was biased toward predicting No Churn. After SMOTE the model learned both classes equally and recall on churners improved significantly from below 50% to 78.6%.


## Threshold Strategy

One of the unique aspects of this project is using **two different thresholds** for two different business use cases.

| Threshold | Value | When to Use | Recall | F1 |
|-----------|-------|-------------|--------|----|
| Default | 0.50 | Standard evaluation | 58.7% | 0.583 |
| **F1-Optimized** | **0.40** | **Individual customer decisions** | 71.5% | 0.618 |
| Business-Optimized | 0.05 | Bulk mass marketing campaigns | 97.0% | 0.493 |

The business threshold (0.05) is intentionally aggressive. It flags almost everyone with any churn risk, maximizing revenue saved at the cost of more false alarms. This makes sense when the cost of missing a churner ($200) is 20x more expensive than a false alarm ($10).


## Risk Scoring System

Instead of a binary Yes/No output, each customer receives a risk score from 0 to 100 with a corresponding action plan.

| Tier | Score | Customers | Actual Churn | Action | Offer |
|------|-------|-----------|--------------|--------|-------|
| 🔴 Critical | 80-100 | 172 (9.8%) | **69.8%** | Personal call within 24hrs | 30% discount 6 months |
| 🟠 High | 60-80 | 196 (11.1%) | **54.1%** | Email + survey this week | 20% discount + upgrade |
| 🟡 Medium | 30-60 | 407 (23.2%) | **37.3%** | Loyalty points this month | Reward points |
| 🟢 Low | 0-30 | 983 (55.9%) | **9.1%** | Newsletter only | Standard promos |

The validation shows each tier has the expected churn rate in the correct order — confirming the scoring system is working accurately.



##  Limitations

| Limitation | Details |
|------------|---------|
| Static data | No time-series component — cannot track how a customer's risk changes over time |
| No product usage data | Call duration, data usage, support tickets would improve predictions significantly |
| US market only | Dataset is from a single fictional US company — may not generalize globally |
| Business threshold caveat | At threshold=0.05, the ML model generates similar value to contacting all customers — the real advantage is in targeting the right people with the right offer |
| No A/B test validation | Retention strategy recommendations are logical but not tested against real business outcomes |
