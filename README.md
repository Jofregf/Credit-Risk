# 📄 Dataset
url: https://www.kaggle.com/datasets/uciml/default-of-credit-card-clients-dataset
##  Columns
* ID: ID of each client
* LIMIT_BAL: Amount of given credit in NT dollars (includes individual and family/supplementary credit)
* SEX: Gender (1=male, 2=female)
* EDUCATION: (1=graduate school, 2=university, 3=high school, 4=others, 5=unknown, 6=unknown)
* MARRIAGE: Marital status (1=married, 2=single, 3=others)
* AGE: Age in years
* PAY_0: Repayment status in September, 2005 
    * -1=pay duly, 
    * 1=payment delay for one month, 
    * 2=payment delay for two months, … 
    * 8=payment delay for eight months, 
    * 9=payment delay for nine months and above
* PAY_2: Repayment status in August, 2005 (scale same as above)
* PAY_3: Repayment status in July, 2005 (scale same as above)
* PAY_4: Repayment status in June, 2005 (scale same as above)
* PAY_5: Repayment status in May, 2005 (scale same as above)
* PAY_6: Repayment status in April, 2005 (scale same as above)
* BILL_AMT1: Amount of bill statement in September, 2005 (NT dollar)
* BILL_AMT2: Amount of bill statement in August, 2005 (NT dollar)
* BILL_AMT3: Amount of bill statement in July, 2005 (NT dollar)
* BILL_AMT4: Amount of bill statement in June, 2005 (NT dollar)
* BILL_AMT5: Amount of bill statement in May, 2005 (NT dollar)
* BILL_AMT6: Amount of bill statement in April, 2005 (NT dollar)
* PAY_AMT1: Amount of previous payment in September, 2005 (NT dollar)
* PAY_AMT2: Amount of previous payment in August, 2005 (NT dollar)
* PAY_AMT3: Amount of previous payment in July, 2005 (NT dollar)
* PAY_AMT4: Amount of previous payment in June, 2005 (NT dollar)
* PAY_AMT5: Amount of previous payment in May, 2005 (NT dollar)
* PAY_AMT6: Amount of previous payment in April, 2005 (NT dollar)
* default.payment.next.month: Default payment (1=yes, 0=no)

# EDA results
* We have 30,000 rows and 25 columns.
* We did not observe any non-null values.
* We observed 78% of people not in default and 22% in default.
* Customers who default have, on average, lower credit limits.
* The average age of people who are in default as those who are not is approximately the same, 35 years, so age does not influence this characteristic.
* Payment history variables (PAY_X) show clear differences between customers with and without defaults. Customers who default have higher average levels of late payments in previous months.
* Customers who default have a significantly higher proportion of recent arrears (1 month or more) compared to customers without default. In particular, the percentage of customers with 2 months of arrears is almost 8 times higher in the default group.
* A significantly higher concentration of recent arrears (1 month or more) is observed in the group that defaults. In particular, the values ​​for 2 months of arrears show a substantial difference between the two groups, suggesting that recent payment behavior is a strong predictor of default risk.
* A progressive increase in the average level of arrears was observed in the months prior to the default, demonstrating a sustained deterioration in payment behavior.
