# kaggle_churn_prediction


In this task, we will be predicting whether a user will churn after their subscription expires. Specifically, we want to see if a user make a new service subscription transaction within 30 days after their current membership expiration date.

As a music streaming service provider, KKBox has members subscribe to their service. When the subscription is about to expire, the user can choose to renew, or cancel the service. They also have the option to auto-renew but can still cancel their membership any time.

The churn/renewal definition can be tricky due to KKBox's subscription model. Since the majority of KKBox's subscription length is 30 days, a lot of users re-subscribe every month. The key fields to determine churn/renewal are transaction date, membership expiration date, and is_cancel. Note that the is_cancel field indicates whether a user actively cancels a subscription. Note that a cancellation does not imply the user has churned. A user may cancel service subscription due to change of service plans or other reasons. The criteria of "churn" is no new valid service subscription within 30 days after the current membership expires.

The train and the test data are selected from users whose membership expire within a certain month. The train data consists of users whose subscription expires within the month of February 2017, and the test data is with users whose subscription expires within the month of March 2017. This means we are looking at user churn or renewal roughly in the month of March 2017 for train set, and the user churn or renewal roughly in the month of April 2017. Train and test sets are split by transaction date, as well as the public and private leaderboard data.

In this dataset, KKBox has included more users behaviors than the ones in train and test datasets, in order to enable participants to explore different user behaviors outside of the train and test sets. For example, a user could actively cancel the subscription, but renew within 30 days.

Tables

train.csv

the train set, containing the user ids and whether they have churned.

msno: user id
is_churn: This is the target variable. Churn is defined as whether the user did not continue the subscription within 30 days of expiration. is_churn = 1 means churn,is_churn = 0 means renewal.
sample_submission_zero.csv

the test set, containing the user ids, in the format that's expected to submit

msno: user id
is_churn: This is what will be predicted. Churn is defined as whether the user did not continue the subscription within 30 days of expiration. is_churn = 1 means churn,is_churn = 0 means renewal.
transactions.csv

transactions of users up until 2/28/2017.

msno: user id
payment_method_id: payment method
payment_plan_days: length of membership plan in days
plan_list_price: in New Taiwan Dollar (NTD)
actual_amount_paid: in New Taiwan Dollar (NTD)
is_auto_renew
transaction_date: format %Y%m%d
membership_expire_date: format %Y%m%d
is_cancel: whether or not the user canceled the membership in this transaction.
user_logs.csv

daily user logs describing listening behaviors of a user. Data collected until 2/28/2017.

msno: user id
date: format %Y%m%d
num_25: # of songs played less than 25% of the song length
num_50: # of songs played between 25% to 50% of the song length
num_75: # of songs played between 50% to 75% of of the song length
num_985: # of songs played between 75% to 98.5% of the song length
num_100: # of songs played over 98.5% of the song length
num_unq: # of unique songs played
total_secs: total seconds played
members.csv

user information. Note that not every user in the dataset is available.

msno
city
bd: age. Note: this column has outlier values ranging from -7000 to 2015, please use judgement.
gender
registered_via: registration method
registration_init_time: format %Y%m%d
expiration_date: format %Y%m%d
Data Extraction Details

One important information in the data extraction process is the definition of membership expiration date. Suppose we have a sequence for a user with the tuple of (transaction date, membership expiration date, and is_cancel):

(2017-01-01, 2017-02-28, false)

(2017-02-25, 0217-03-15, false)

(2017-04-30, 3017-05-20, false)

(data used for demo only, not included in competition dataset)

This user is included in the dataset since the expiration date falls within our time period. Since the subscription transaction is 30 days away from 2017-03-15, the previous expiration date, we will count this user as a churned user.

Let's consider a more complex example derive the last one, suppose now a user has the following transaction sequence

(2017-01-01, 2017-02-28, false)

(2017-02-25, 2017-04-03, false)

(2017-03-15, 2017-03-16, true)

(2017-04-01, 3017-06-30, false)

The above entries is quite typical for a user who changes his subscription plan. Entry 3 indicates that the membership expiration date is moved from 2017-04-03 back to 2017-03-16 due to the user making an active cancellation on the 15th. On April 1st, the user made a long term (two month subscription), which is 15 days after the "current" expiration date. So this user is not a churn user.

Now let's consider the a sequence that indicate the user does not falls in our scope of prediction

(2017-01-01, 2017-02-28, false)

(2017-02-25, 2017-04-03, false)

(2017-03-15, 2017-03-16, true)

(2017-03-18, 2017-04-02, false)

Note that even the 3rd entry has member ship expiration date falls in 2017-03-16, but the fourth entry extends the membership expiration date to 2017-04-02, not between 2017-03-01 and 2017-03-31, so we will not make a prediction for the user.
