# Improve Authorization Rate

### Overview

Millions of transactions are processed globally every day for a large group of merchants.
Not all payments are accepted. Some are declined without a reason and others are fraudulent transactions.

---

### Problem Statement

We want to help merchants to optimize the conversion and acceptance of payments to increase their revenues.

In order to do so, we would first have to understand what causes declined transactions and frauds, and what factors helps to improve acceptance rate.

---

### Data Cleaning

* There are 232 empty cells under 'issuercountrycode' and they are imputed with 'NA'.
* 'creationdate' date is split into 'year', 'month', 'date', 'day' and 'hour' for our analysis purpose.
* It was later discovered that there is only one year, '2017' and one month 'October'.<br>
Hence the year and month columns are dropped
* The dataset is then split into train set and test set to prevent overspill
* All the analysis are done on the train set

---

### Exploratory Data Analysis

#### Total Number of Transaction
There are a total of 181,494 transactions in the train set

#### Generic Response
'Generic Response' tells us if the transaction has been approved, declined, or rejected due to other reasons.

Declined transactions is the third largest group after fraud.
It is therefore crucial to know what causes transactions to be declined

|    genericresponse   |   %   | 
|:--------------------:|:-----:|
| APPROVED             | 88.25 |
| FRAUD                | 79.90 | 
| DECLINED             | 29.83 |
| NOT_3D_AUTHENTICATED | 42.32 | 
| CVC_DECLINED         | 11.24 | 

#### Merchant's Fraud and Declined Rate
It is noted that merchants that have higher fraud rate tends to also have higher declined rate.
Example, ABC_IDR has the highest fraud rate of 3.10% and the highest declined rate of 1.43%.

In order to reduce declined rate so as to increase acceptance rate, merchants should target to reduce fraud transactions.

| merchant | fraud_rate (%) | declined_rate (%) |
|:--------:|:--------------:|:-----------------:|
| ABC_IDR  |    3.101480    |      1.429799     |
| DEF_IDR  |    1.141636    |      0.467784     |
| XYZ_IDR  |    1.095904    |      0.482110     |
| ABC_SGD  |    0.996176    |      0.052894     |
| ABC_MYR  |    0.501394    |      0.065567     |
| XYZ_MYR  |    0.463927    |      0.079341     |
| XYZ_SGD  |    0.222046    |      0.038018     |
| DEF_SGD  |    0.207169    |      0.024794     |
| DEF_THB  |    0.135542    |      0.138848     |
| XYZ_THB  |    0.124522    |      0.203313     |


**Note**:
The analysis will be looking at the general trend across all merchants instead of drilling into individual merchants.


#### % of 3DS Authentication Attempt for each Generic Response Category
No 3DS authentication was attempted for 100% of fraud transactions.

It is highly recommended that merchants introduce some sort of 3DS authentication.
It could be requesting for authentication for targeted payment methods for transaction amount over a certain values.

| genericresponse 	| threedattempt 	|   %   	|
|:---------------:	|:-------------:	|:-----:	|
|     APPROVED    	|       No      	| 47.70 	|
|     APPROVED    	|      Yes      	| 40.55 	|
|      FRAUD      	|       No      	|  7.99 	|
|     DECLINED    	|      Yes      	| 1.829 	|
|     DECLINED    	|       No      	|  1.16 	|


#### Payment Method
The top three payment method for fraud transactions and the top three payment method for approved transactions are the same, with varying percentage and ranking.

|  paymentmethod 	|   Fraud  	| Approved 	|
|:--------------:	|:--------:	|:--------:	|
|  standarddebit 	| 0.438453 	| 0.174099 	|
| standardcredit 	| 0.201917 	| 0.217873 	|
|  premiumcredit 	| 0.151576 	| 0.249547 	|

#### Mean Amount for Top Three Payment Method
Mean transaction amount of approved transactions is generally higher than Mean transaction amount of fraud transactions.

It is not wise to set 3DS authentication for amount lower than the current mean approved transaction amount, as this might disrupt customers' payment experience.

As such, it is recommended that merchants implement 3DS2 authentication, which is a more frictionless authentication process that will help to prevent fraud and provide seamless payment experience for customers.

| currencycode 	|  paymentmethod 	| mean_fraud_amount 	| mean_approved_amount 	|
|:------------:	|:--------------:	|:-----------------:	|:--------------------:	|
|      IDR     	|  premiumcredit 	|    15766.666667   	|     25496.007041     	|
|      IDR     	| standardcredit 	|    14587.905237   	|     25264.182011     	|
|      IDR     	|  standarddebit 	|    5023.552460    	|     20309.600439     	|
|      MYR     	|  premiumcredit 	|     658.910891    	|      1485.282286     	|
|      MYR     	| standardcredit 	|     640.788382    	|      1486.678190     	|
|      MYR     	|  standarddebit 	|     561.673004    	|      1168.750000     	|
|      SGD     	|  premiumcredit 	|    3516.719745    	|      1447.420622     	|
|      SGD     	| standardcredit 	|    3686.075949    	|      1434.996311     	|
|      SGD     	|  standarddebit 	|    2656.311745    	|      1321.058615     	|
|      THB     	|  premiumcredit 	|    11315.217391   	|     11248.350286     	|
|      THB     	| standardcredit 	|    2625.980392    	|     13529.739597     	|
|      THB     	|  standarddebit 	|    6896.875000    	|      11391.81563     	|


**Note**:
It is assumed that fraud can happen at any day and at any time. Therefore we will not dive into analysis for day and hour.

The analysis for the following sections will be based on declined transactions.


#### Transacted Currency, Acquirer Account currency and Issuer Account Currency 
For majority of the declined transactions, there is no difference in the acquirer account currency, issuer account currency and the transacted currency.
It is noted that the is a small percentage of such incidents, however, it is not huge enough for us to deep dive into it.

|          acquireraccount         	| currencycode 	| issuercountrycode 	| % of Declined Transactions 	|
|:--------------------------------:	|:------------:	|:-----------------:	|:--------------------------:	|
| AdyenMasterCardMalaysia_ABC_IDR  	|      IDR     	|         ID        	|          0.255496          	|
| AdyenMasterCardMalaysia_XYZ_IDR  	|      IDR     	|         ID        	|          0.142435          	|
| AdyenMasterCardEurope_DEF_IDR    	|      IDR     	|         ID        	|          0.136708          	|
| AdyenMasterCardSingapore_ABC_IDR 	|      IDR     	|         ID        	|          0.126917          	|
| AdyenMasterCardEurope_XYZ_THB    	|      THB     	|         TH        	|          0.035101          	|
| AdyenVisaMalaysia_ABC_IDR        	|      IDR     	|         ID        	|          0.022908          	|
| AdyenMasterCardSingapore_DEF_THB 	|      THB     	|         TH        	|          0.017735          	|
| AdyenVisaMalaysia_XYZ_MYR        	|      MYR     	|         MY        	|          0.013301          	|
| AdyenVisaMalaysia_ABC_MYR        	|      MYR     	|         MY        	|          0.012747          	|
| AdyenVisaEurope_XYZ_THB          	|      THB     	|         TH        	|          0.011269          	|


#### Payment Method of Declined Transactions
Premium Credit and Standard Credit make up almost half (46.74%) of the payment type for approved transactions.
The most common payment method for declined transactions is Standard Debit.
Standard Debit is the third most common payment method for approved transactions making up to 17.41% of total approved transactions.
Therefore it is worth taking a look at the Standard Debit payment type to see what factors affects the approval/declined rates.

The following sections will focus on transactions made with Standard Debit.


#### 3DS Authentication Rate
Percentage of transaction without 3D authentication attempt is about the same for both approved and declined transaction.

| 3D attempt 	| Declined 	| Approved 	|
|:----------:	|:--------:	|:--------:	|
|     Yes    	| 0.648562 	|  0.67372 	|
|     No     	| 0.351438 	|  0.32628 	|


#### 3D Attempt vs Amount
The mean transacted amount (15,572.29) for approved transactions without 3D authentication attempt is much lower than the mean amount (51,222.20) for declined transaction for standard debit payment method.
It is likely that the amount is a factor causing the transactions to be declined for Standard Debit payment method.
Hence, suggest to implement 3D authentication for Standard Debit payment method.

| 3D attempt 	| Mean Declined Transactions Amount 	| Mean Approved Transactions Amount 	|
|:----------:	|:---------------------------------:	|:---------------------------------:	|
|     No     	|            52045.247107           	|            15480.272587           	|
|     Yes    	|             980.295567            	|            1146.248217            	|


#### 3D Attempt, Amount and Currency
The mean approved transacted amount without 3D authentication attempt is lower than the mean declined transacted amount for each currency for standard debit payment method.

It is recommended that merchants implement 3DS authentication for transaction amount higher than the mean approved transacted amount.

| 3D attempt 	| Mean Declined Transactions Amount 	| Mean Approved Transactions Amount 	|
|:----------:	|:---------------------------------:	|:---------------------------------:	|
|     IDR    	|            53983.361646           	|            20309.600439           	|
|     THB    	|            26885.416667           	|            11391.815631           	|
|     SGD    	|            1762.500000            	|            1321.058615            	|
|     MYR    	|                                   	|            1168.750000            	|


#### Calendar Day
The top three calendar day for declined transactions are:
1. 18
2. 26
3. 27

Merchants should avoid routing standard debit payments on these dates.


The top three calendar day for declined transactions are:
1. 20
2. 29
3. 28

Merchants should route standard debit payments on these dates.


#### Calendar Day x Hour
The 18th and 19th hour are two common hours with high number of declined and approved transactions.
Of the declined transactions settled at the 18th and 19th hour, 9.7% of it fall on the 18th calendar day, followed by 27th and 13th with 6% each.
Hence, if merchants have route transactions at these hours, they should avoid doing it on the 18th, 27th and 13th of the month.

### Day of the Week
Similar day of the week is observed for both declined and approved transactions.

The top three days where there were declined and approved transactions is:
Mon, Tue and Sun

| day_of_week 	| % of Declined Transactions 	| % of Approved Transactions 	|
|:-----------:	|:--------------------------:	|:--------------------------:	|
|     Mon     	|          0.167296          	|          0.161168          	|
|     Tue     	|          0.162649          	|          0.161849          	|
|     Sun     	|          0.151322          	|          0.159912          	|


#### Day of the Week x Hour
Of the declined transactions that happened on Mon, Tue or Sun:
* 2.6% are on Tue, 12pm
* 2.6% are on Mon, 3pm
* 2.5% are on Tue, 4pm

Merchants should avoid scheduling payments on these days and timings.


Of the approved transactions that happened on Mon, Tue or Sun:
* 2.7% are on Mon, 7pm
* 2.5% are on Sun, 2pm
* 2.4% are on Mon, 9am

Merchants should schedule payments on these days and timings.

---

### Base Model

#### Feature Selection
Through the earlier analysis, the features that seem to impact the authorization rate are:
* payment method
* 3D attempt
* amount
* calendar day
* hour
* day of week

We will build a simple logistic regression model using the above 5 features.

#### Accuracy Score
Train Set Accuracy: 0.8824<br>
Test Set Accuracy: 0.8814

The logistic regression model confirms with a 88% acurracy rate that our earlier analysis that 'payment method', '3D attempt', 'calendar day', 'hour' and 'day of the week' are important factors that influence the acceptance rate.


#### RSME
Training RMSE: 0.6082<br> 
Testing RMSE: 0.6037


#### Coefficient
**Approved Transactions**
|    feature    	|    coef   	| absolute coef 	|
|:-------------:	|:---------:	|:-------------:	|
|     amount    	|  0.696473 	|    0.696473   	|
| threedattempt 	| -0.194877 	|    0.194877   	|
|      hour     	|  0.118909 	|    0.118909   	|
|  day_of_week  	| -0.096571 	|    0.096571   	|
| paymentmethod 	|  0.012878 	|    0.012878   	|
|      day      	| -0.010195 	|    0.010195   	|

**Fraud Transactions**
|    feature    	|    coef   	| absolute coef 	|
|:-------------:	|:---------:	|:-------------:	|
| threedattempt 	| -4.808873 	|    4.808873   	|
|  day_of_week  	| -0.089575 	|    0.089575   	|
| paymentmethod 	|  0.070181 	|    0.070181   	|
|     amount    	|  0.022404 	|    0.022404   	|
|      hour     	|  0.022086 	|    0.022086   	|
|      day      	|  0.021720 	|    0.021720   	|

'3D attempt' has the highest absolute coefficient of 4.81.
Hence, it is important for merchants to implement 3DS2 authentication to help prevent fraud.

**Declined Transactions**
|    feature    	|    coef   	| absolute coef 	|
|:-------------:	|:---------:	|:-------------:	|
|     amount    	|  0.947852 	|    0.947852   	|
| threedattempt 	|  0.224769 	|    0.224769   	|
|  day_of_week  	| -0.122675 	|    0.122675   	|
|      hour     	|  0.073842 	|    0.073842   	|
|      day      	|  0.069388 	|    0.069388   	|
| paymentmethod 	| -0.030834 	|    0.030834   	|

The most important factor for declined transactions is amount followed by 3D attempt.
By having 3DS authentication for transaction over a certain amount would help reduce the declined rate.


**Note:**
This is a base model and has more room for improvement, where we can tune the model to increase accuracy rate.

---

### Conclusion

In conclusion, in order to increase the authorization rate, it is highly recommended that the merchants implement either 3DS or 3DS2 authentication. increase authorization rate.<br>
In addition, merchants should avoid scheduling payments on certain calendar day, day of the week and hour to reduce declined rates.# increase_payment_authorization_rate
