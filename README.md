# Udacity-AB-Testing-Final-Project
My solution to the final project of AB Testing course on Udacity 

## Experiment Overview
At the time of this experiment, Udacity courses currently have two options on the course overview page: "start free trial", and "access course materials". If the student clicks "start free trial", they will be asked to enter their credit card information, and then they will be enrolled in a free trial for the paid version of the course. After 14 days, they will automatically be charged unless they cancel first. If the student clicks "access course materials", they will be able to view the videos and take the quizzes for free, but they will not receive coaching support or a verified certificate, and they will not submit their final project for feedback.

In the experiment, Udacity tested a change where if the student clicked "start free trial", they were asked how much time they had available to devote to the course. If the student indicated 5 or more hours per week, they would be taken through the checkout process as usual. If they indicated fewer than 5 hours per week, a message would appear indicating that Udacity courses usually require a greater time commitment for successful completion, and suggesting that the student might like to access the course materials for free. At this point, the student would have the option to continue enrolling in the free trial, or access the course materials for free instead. This screenshot shows what the experiment looks like as below.

![Final Project_ Experiment Screenshot](https://user-images.githubusercontent.com/88932816/160266145-f0641ccf-1920-45ff-b494-be02749411d7.png)

The **hypothesis** was that this might set clearer expectations for students upfront, thus reducing the number of frustrated students who left the free trial because they didn't have enough time—without significantly reducing the number of students to continue past the free trial and eventually complete the course. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.

## Metric Choice
Below are available metrics in this case. The practical significance boundary for each metric, that is, the difference that would have to be observed before that was a meaningful change for the business, is given in parentheses. All practical significance boundaries are given as absolute changes.

PS: Any place "unique cookies" are mentioned, the uniqueness is determined by day. (That is, the same cookie visiting on different days would be counted twice.) User-ids are automatically unique since the site does not allow the same user-id to enroll twice.

**Number of cookies**: That is, number of unique cookies to view the course overview page. (dmin=3000)

**Number of user-ids**: That is, number of users who enroll in the free trial. (dmin=50)

**Number of clicks**: That is, number of unique cookies to click the "Start free trial" button (which happens before the free trial screener is trigger). (dmin=240)

**Click-through-probability**: That is, number of unique cookies to click the "Start free trial" button divided by number of unique cookies to view the course overview page. (dmin=0.01)

**Gross conversion**: That is, number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button. (dmin= 0.01)

**Retention**: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by number of user-ids to complete checkout. (dmin=0.01)

**Net conversion**: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Start free trial" button. (dmin= 0.0075)


### Choosing Invariant Metrics:
Number of cookies: Since the experimental change of Free Trial Screener happens after users clicking “start free trial” button, so should be no impact on the number of cookies

Number of clicks: Since the experimental change of Free Trial Screener happens after users clicking “start free trial” button, so should be no impact on the number of clicks

Click-through-probability: Since the experimental change of Free Trial Screener happens after users clicking “start free trial” button, so should be no impact on Click-through-probability


### Choosing Evaluation Metrics:
Since the goal of this feature change was to reduce the number of frustrated students who left the free trial because they didn't have enough time, evaluation metrics should be those that can monitor whether users continue to complete the courses then make a payment. So the evaluation metrics can be:

Gross conversion: That is, number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button. Adding the free trial screener will provide an extra option to students who cannot commit enough time to choose to continue enrolling in the free trial, or access the course materials for free instead. So the gross conversion rate might decrease.

Retention: This metric is to evaluate among users who completed checkout process, i.e., started a free trail, how many of them remained enrolled for 14 days and make at least one payment. In order to launch the experiment, I would expect this metric to increase, showing an improved student experience. 

Net conversion: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Start free trial" button. By definition, Net conversion = gross conversion * retention, so it’s an composite metrics based on the other two.

## Calculating standard deviation
For each metric selected as an evaluation metric, with a sample size of 5000 cookies visiting the course overview page, their analytical standard deviation can be calculated as below using [baseline values](https://docs.google.com/spreadsheets/d/1MYNUtC47Pg8hdoCjOXaHqF-thheGpUshrFA21BAJnNc/edit#gid=0).


|Unique cookies to view course overview page per day:|	40000|
|Unique cookies to click "Start free trial" per day:|	3200|
|Enrollments per day:|	660|
|Click-through-probability on "Start free trial":|	3200/40000=0.08|
|Probability of enrolling, given click: (gross conversion)|	660/3200=0.20625|
|Probability of payment, given enroll: (retention)|	0.53|
|Probability of payment, given click: (net conversion)|	0.53*0.20625=0.1093125|

std of binomial distribution = sqrt(p*(1-p)/n)

Gross convention: 
n= 3200/40000*5000=400
Std = sqrt(0.20625*(1-0.20625)/400) = 0.0202

Retention: 
n= 660/40000*5000=82.5
Std = sqrt(0.53*(1-0.53)/82.5) = 0.0549

Net conversion: 
n= 3200/40000*5000=400
Std = sqrt(0.1093125*(1-0.1093125)/400) = 0.0156

## Sizing
### Number of Samples vs. Power

Will you use the Bonferroni correction?
> No, because these evaluation metrics are related to each other, Bonferroni correction would be too conservative.

Which evaluation metrics did you choose?
> gross conversion, retention, net conversion


How many pageviews will you need?
> I would use this online [calculator](https://www.evanmiller.org/ab-testing/sample-size.html) to estimate sample size;

parameters input:
Gross convention: 
Baseline conversion rate: 20.625%
Minimum Detectable Effect: 0.01 (dmin)
Sample size: 25,835 (for each group)
So the total sample size = 25,835 *2 = 51,670
Since the sample is for gross conversion, the unit is clicks. To get 51,670 clicks, we would need 51,670 / (3200/40000) =  645,875 pageviews

Retention: 
Baseline conversion rate: 53%
Minimum Detectable Effect: 0.01 (dmin)
Sample size: 39,115 (for each group)
So the total sample size = 39,115*2 = 78,230
Since the sample is for retention, the unit is enrollment. To get 78,230 enrollments, we would need 78,230 / (660/40000) =  4,741,212 pageviews

Std = sqrt(0.53*(1-0.53)/82.5) = 0.0549

Net conversion: 
Baseline conversion rate: 10.93125%
Minimum Detectable Effect: 0.0075 (dmin)
Sample size: 27,413 (for each group)
So the total sample size = 27,413 *2 = 54,826
Since the sample is for net conversion, the unit is clicks. To get 54,826 clicks, we would need 54,826 / (3200/40000) =  685,275 pageviews


The maximum pageviews is 4,741,212, so we need this number of pageviews for three metrics.


### Duration vs. Exposure
Indicate what fraction of traffic you would divert to this experiment and, given this, how many days you would need to run the experiment. (These should be the answers from the "Choosing Duration and Exposure" quiz.)

Given 40,000 page views per day, for current goal of 4,741,212 page views, if we divert 100% of traffic, the experiment should run about 119 days, almost 4 months, which is a pretty long experiment, and it’s also risky to divert all traffic to the experiment because the new feature might not run well 100%.

To reduce the duration, we can eliminate retention, only requiring 685,275 pageviews, running experiment on 35 days with 50% diversion. Other duration and exposure% also works here.


## Experiment Analysis
### Sanity Checks
For each of your invariant metrics, give the 95% confidence interval for the value you expect to observe, the actual observed value, and whether the metric passes your sanity check. For any sanity check that did not pass, explain your best guess as to what went wrong based on the day-by-day data. Do not proceed to the rest of the analysis unless all sanity checks pass.
Control and experiment groups should be comparable, and they shouldn’t change when running experiment. 
The experiment data can be found [here](https://docs.google.com/spreadsheets/d/1Mu5u9GrybDdska-ljPXyBjTpdZIUev_6i7t4LRDfXM8/edit#gid=0)

For a count, you should calculate a confidence interval around the fraction of events you expect to be assigned to the control group, and the observed value should be the actual fraction that was assigned to the control group. 
For any other type of metric, (such as probability), you should construct a confidence interval for a difference in proportions, then check whether the difference between group values falls within that confidence level.


Number of cookies:
Observed value = Number of pageviews in control group/Number of pageviews in exp group = 345543/(345543+344660) = 0.5006
SE = sqrt(p(1-p)/n) = sqrt(0.5*(1-0.5)/(345543+344660)) = 0.0006
Margin of error = 1.96*0.0006= 0.0012
Lower bound = 0.5-0.0012=0.4988
Upper bound = 0.5+0.0012=0.5012
Observed value 0.5006 is within the 95% confidence interval around 0.5, so it passes sanity check.

Number of clicks: 
Observed value = Number of clicks in control group/Number of clicks in exp group = 28378/(28378+28325) = 0.5005
SE = sqrt(p(1-p)/n) = sqrt(0.5*(1-0.5) /(28378+28325)) = 0.0021
Margin of error = 1.96*0.0021= 0.0041
Lower bound = 0.5-0.0041=0.4959
Upper bound = 0.5+0.0041=0.5041
Observed value 0.5005 is within the 95% confidence interval around 0.5, so it passes sanity check.


Click-through-probability: 
Observed value = CTP_exp - CTP_con = 28325/344660 – 28378/345543=0.0001
pooled_CTP = (28325+28378)/( 344660+345543) = 0.0822
pooled_SE = sqrt(0.0822*(1-0.0822)*(1/344660+1/345543)) = 0.0007
Margin of error = 1.96*0.0007= 0.0013
Lower bound = -0.0013
Upper bound = 0.0013
Observed value 0.0001 is within the 95% confidence interval around difference 0, so it passes sanity check.

## Result Analysis
### Effect Size Tests
For each of your evaluation metrics, give a 95% confidence interval around the difference between the experiment and control groups. Indicate whether each metric is statistically and practically significant.

Gross conversion: 
Gross conversion_control = 3785/17293= 0.2189
Gross conversion_exp= 3423/17260= 0.1983 # Since enrollments only have 23 days records, so make sure only include 23 days click records
D_hat = 0.1983-0.2189=-0.0206
Pooled_gross conversion = (3785+3423)/(17293+17260)=0.2086
Pooled_SE = sqrt(0.2086*(1-0.2086)*(1/17293+1/17260) = 0.0044
Margin of error = 1.96*0.0044 = 0.0086
Lower bound = -0.0206-0.0086 = -0.0291
Upper bound = -0.0206+0.0086 = -0.0120
The Confidence interval doesn’t include 0, so it is statistically significant, given dmin = 0.01, C.I. also doesn’t include -0.01 or 0.01, so it’s also practically significant. 


Retention: 
retention_control = 2033/3785= 0.5371
retention_exp= 1945/3423= 0.5682
D_hat = 0.5682-0.5371 = 0.0311
Pooled_gross conversion = (2033+1945)/( 3785+3423)=0.5519
Pooled_SE = sqrt(0.5519*(1-0.5519)*(1/3785+1/3423) = 0.0117
Margin of error = 1.96*0.0117= 0.0230
Lower bound = 0.0311-0.0230 = 0.0081
Upper bound = 0.0311+0.0230 = 0.0541
The Confidence interval doesn’t include 0, so it is statistically significant, given dmin = 0.01, C.I. does include 0.01, so it’s not practically significant. 


Net conversion: 
retention_control = 2033/17293= 0.1176
retention_exp= 1945/17260= 0.1127
D_hat = 0.1127 - 0.1176= -0.0049
Pooled_gross conversion = (2033+1945)/( 17293+17260)=0.1151
Pooled_SE = sqrt(0.1151*(1-0.1151)*(1/17293+1/17260) = 0.0034
Margin of error = 1.96*0.0034= 0.0067
Lower bound = -0.0049-0.0067 = -0.0116
Upper bound = -0.0049+0.0067 = 0.0019
The Confidence interval includes 0, so it is not statistically significant. Given dmin = 0.0075, C.I. does include -0.0075, so it’s not practically significant. 

### Sign Tests
For each of your evaluation metrics, do a sign test using the day-by-day data, and report the p-value of the sign test and whether the result is statistically significant. (These should be the answers from the "Sign Tests" quiz.)
I would use this online [calculator](https://www.graphpad.com/quickcalcs/binomial1.cfm#:~:text=The%20sign%20test%20is%20a,two%20outcomes%20have%20equal%20probabilities.&text=You%20will%20compare%20those%20observed%20results%20to%20hypothetical%20results) to calculate p-value:

Gross conversion: 
Calculate gross conversion of each day for both control and experiment group, compare whether experiment group is greater than control group. 
It shows that 4 days among 23 days that exp > control, so input number of successes = 4, number of trials = 23, probability = 0.5 -> The two-tail P value is 0.0026
P-value < 0.05, so it is statistically significant

Retention:
Calculate retention of each day for both control and experiment group, compare whether experiment group is greater than control group. 
It shows that 13 days among 23 days that exp > control, so input number of successes = 13, number of trials = 23, probability = 0.5 -> The two-tail P value is 0.6776
P-value > 0.05, so it is not statistically significant

Net conversion:
Calculate gross conversion of each day for both control and experiment group, compare whether experiment group is greater than control group. 
It shows that 10 days among 23 days that exp > control, so input number of successes = 10, number of trials = 23, probability = 0.5 -> The two-tail P value is 0.6776
P-value > 0.05, so it is not statistically significant

From the sign test, gross conversion and net conversion has the same indication as effect size tests, while results of retention is reverse. From this case, I would still rely more on the effect size tests instead of sign test because sign tests have lower power, but it still worth digging deeper.


## Recommendation:

Gross conversion (given click, probability of enrollment) both statistically and practically decreases, which is what we expected to see. However, we also expected the retention to increase, that is given enroll, higher proportion of students can complete the courses and make payments, but the results show that retention does have a statistically significant increase but not so meaningful to business. So my recommendation would be not to launch.








