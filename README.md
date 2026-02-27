# Credit Risk Analysis - Default Prediction

## 📄 Dataset
That dataset is the famous dataset of the UCI Machine Learning Repository about credit card customers in Taipei, originally published by researchers in Taiwan.

### [Link](https://www.kaggle.com/datasets/uciml/default-of-credit-card-clients-dataset)

---
###  Main Columns
|          Columns         |                                       Description                                        |
|--------------------------|------------------------------------------------------------------------------------------|
|ID|ID of each client|
|LIMIT_BAL|Amount of given credit in NT dollars (includes individual and family/supplementary credit)|
|SEX|Gender (1=male, 2=female)|
|EDUCATION|(1=graduate school, 2=university, 3=high school, 4=others, 5=unknown, 6=unknown)|
|MARRIAGE|Marital status (1=married, 2=single, 3=others)|
|AGE|Age in years|
|PAY_0 -> PAY_6|payment status for the last 6 months (-1=pay duly, 1=payment delay for one month, 2=payment delay for two months, 8=payment delay for eight months, 9=payment delay for nine months and above)|
|BILL_AMT1 -> BILL_AMT6|Amount billed per month (NT dollars)|
|PAY_AMT1 -> PAY_AMT6|paid per month (NT dollars)|
|default.payment.next.month|Target variable: 1 = default (delay > 1 month), 0 = no default|

---
## 🔎 Exploratory Data Analysis (EDA)
### 1️⃣ Target Distribution
- Total rows: 30,000
- Total columns: 25
- Null data: none
- Default distribution: **78% non-default / 22% default**

---

### 2️⃣ Age & Credit Limit
- Average credit limit:
    - No default: 178,099
    - Default: 130,110
- Average age: ~35 years for both groups
**Insight:** Age shows limited discriminatory power in default prediction.

---
### 3️⃣ Payment History (PAY_0 → PAY_6)
- Customers with defaults show a **progressive worsening** in recent payments.
- The correlation increases progressively from PAY_6 to PAY_0, confirming that **recent behavior is more important than distant historical behavior**.
**Suggested graph:** Evolution of PAY_6 → PAY_0 by group
![PAY evolution](./reports/figures/pay_evolution.png)

---
### 4️⃣ Billing & Payment Amounts
- Similar average invoice amounts across groups.
- Customers who defaulted have significantly lower payments in all months.
- Example (median PAY/BILL ratio):

|          Ratio          | No default | Default |
--------------------------|------------|---------|
Median PAY/BILL (month 1) |    0.056   |  0.046  |

**Insight:** The ability or willingness to pay is key, more so than the total debt.

---
### 5️⃣ Payment Ratios
- We calculate `PAY_RATIO = PAY_AMT / BILL_AMT`
- PAY_0 is the most predictive variable with a correlation of **0.32**.
- Customers with defaults consistently have lower ratios, indicating a reduced capacity to cover accumulated debt.
- This insight is confirmed by the correlation matrix:
![Correlation Matrix](./reports/figures/correlation_matrix.png)
- This highlights the importance of behavioral frequency over magnitude-based metrics.

---
### 6️⃣ Key Predictive Features
- PAY_0 → most recent behavior
- PAY_0 → PAY_6 → deteriorating trend
- Payment ratios → relative payment capacity
- LIMIT_BAL → slight difference
- AGE → little effect

**Conclusion:**
Recent payment behavior and the proportion of payments relative to the invoice are the most decisive factors for predicting default.

---
## 🛠️ Feature Engineering
### 1️⃣ PAY_LATEST
We created `PAY_LATEST` as an explicit feature representing the most recent repayment status (September 2005).
This variable is equivalent to `PAY_0` but renamed to reflect its semantic meaning within the feature engineering process.
Correlation with target:
- PAY_LATEST vs default: **0.325**

This confirms that the most recent payment behavior is the strongest individual predictor of default risk, reinforcing the EDA findings.

---
### 2️⃣ PAY_MEAN
We created `PAY_MEAN` as the average repayment status across the last six months.
This feature captures the overall historical payment behavior of each customer, smoothing month-to-month fluctuations.
Correlation with target:
- PAY_MEAN vs default: **0.282**

Although slightly lower than PAY_LATEST, this variable remains strongly predictive, indicating that sustained payment deterioration over time is an important risk factor.

---
### 3️⃣ PAY_MAX_DELAY
We created `PAY_MAX_DELAY` to capture the worst repayment status observed in the last six months.
This feature represents the maximum delay experienced by each customer, highlighting extreme delinquency events.
Correlation with target:
- PAY_MAX_DELAY vs default: **0.331**

This is the strongest individual correlation observed so far, suggesting that severe historical delinquency events are highly predictive of future default risk.

---
### 4️⃣ PAY_COUNT_DELAY_1plus & PAY_COUNT_DELAY_2plus
We created frequency-based features to capture how often customers experienced delinquency over the last six months.
- `PAY_COUNT_DELAY_1plus`: Number of months with delay ≥ 1.
- `PAY_COUNT_DELAY_2plus`: Number of months with delay ≥ 2.
Correlations with target:
- PAY_COUNT_DELAY_1plus vs default: **0.398**
- PAY_COUNT_DELAY_2plus vs default: **0.399**

These are the strongest correlations observed so far, suggesting that repeated delinquency is a more powerful predictor of default than a single extreme event or the most recent status alone.

---
### 5️⃣ PAY_TREND
We created `PAY_TREND` as the difference between the most recent and the oldest repayment status:
PAY_TREND = PAY_0 − PAY_6
This feature captures whether the customer's payment behavior has deteriorated or improved over time.
Correlation with target:
- PAY_TREND vs default: **0.129**

Although the direction of change has some predictive value, it is significantly weaker than frequency or severity-based features. This suggests that the absolute level and repetition of delinquency are more important predictors than short-term behavioral trends.

---
### 6️⃣ PAYMENT RATIO - LAST MONTH
We first engineered a feature measuring the ratio between payment amount and billed amount for the most recent month:
`PAY_RATIO_LATEST = PAY_AMT1 / BILL_AMT1`
Result:
* Correlation with default: ≈ -0.006
* Interpretation: No meaningful linear relationship detected.

Conclusion:
The most recent payment proportion alone does not provide predictive signal.

---
### 7️⃣ Average Payment Ratio – Six-Month Window
We then computed the mean payment ratio across six months, excluding months with no outstanding balance (to avoid distortions caused by zero or negative bills).
Result:
* Correlation with default: ≈ -0.01
* Interpretation: Average repayment level does not significantly explain default risk.

Conclusion:
Continuous repayment magnitude appears weakly related to future default.

--- 
### 8️⃣ Behavioral Feature – Persistent Underpayment
Instead of focusing on continuous averages, we engineered a behavioral feature:
Definition:
Proportion of months where the client paid less than 50% of the billed amount (considering only months with positive balance).
Result:
* Correlation with default: 0.1086

Interpretation:
Repeated underpayment behavior shows a significantly stronger relationship with default risk compared to simple ratio averages.

---
### 9️⃣ Zero Payment Frequency
We engineered a feature capturing the proportion of months in which the client made no payment at all, considering only months with positive outstanding balance.
Definition:
`PROP_MONTHS_ZERO_PAYMENT`
Result:
* Correlation with default: 0.159

Interpretation:
Complete absence of payment is significantly more predictive than partial underpayment.
This suggests that extreme negative repayment behavior carries stronger risk signal than moderate repayment deterioration.

---
*** 🔟 Overpayment Frequency
We engineered a feature measuring the proportion of months where the client paid more than 100% of the billed amount.
Definition:
`PROP_MONTHS_OVERPAYMENT`
Result:
* Correlation with default: -0.111

Interpretation:
Frequent overpayment behavior is associated with lower default risk, although the protective signal is weaker than the risk signal from zero payments.

This asymmetry reflects a common credit risk pattern:
Negative extreme behavior tends to be more informative than positive behavior.

---
1️⃣1️⃣ PAY_DISCIPLINE_SCORE (Behavioral Composite Score)
To integrate repayment behavior into a single interpretable metric, we designed a weighted behavioral score combining positive and negative repayment signals. Zero payment months were weighted more heavily due to their stronger empirical correlation and higher risk severity interpretation.:

```python
PAY_DISCIPLINE_SCORE = (
    + 1.0 * PROP_MONTHS_OVERPAYMENT
    - 1.0 * PROP_MONTHS_LOW_PAYMENT
    - 1.5 * PROP_MONTHS_ZERO_PAYMENT
)
```

Result:
* Correlation with default: -0.1805

Interpretation:
The composite behavioral score shows stronger predictive power than any individual repayment ratio feature.
This confirms that combining frequency and severity signals enhances risk detection.

The score captures:
* Persistent negative repayment behavior
* Extreme non-payment events
* Positive repayment discipline

---
🔎 Key Insights

Repeated delinquency (frequency of delay) is the strongest predictor of default.

Extreme repayment behavior (zero payments) carries stronger signal than partial underpayment.

Continuous financial ratios (average payment proportions) provide limited predictive value.

Behavioral aggregation through weighted scoring significantly improves signal strength.

These findings suggest that default risk is primarily driven by behavioral consistency and delinquency frequency, rather than by isolated financial magnitude metrics.

---
🎯 Strategic Takeaway

This project demonstrates that:

Hypotheses must be validated empirically.

Not all financially intuitive features provide predictive value.

Frequency-based and behaviorally aggregated features outperform naive ratio-based metrics.

Composite behavioral scoring can meaningfully enhance risk signal strength.

The iterative experimentation process mirrors real-world credit risk modeling practices.

---
