---
title: "Greenpeace Insight Skillshare"
author: "Martin Hou"
date: '21 November, 2016'
output:
  ioslides_presentation:
    <!--css: styles.css -->
    incremental: yes
    logo: sb7ixc5z.png
    transition: default
    widescreen: yes
  slidy_presentation:
    incremental: yes
job: Data Specialist
logo: sb7ixc5z.png
license: by-nc-sa
mode: selfcontained
hitheme: tomorrow
highlighter : highlight.js
subtitle: R Predictive Modelling
framework: io2012
widgets: [shiny, interactive]
---

<style>
pre code, pre, code {
  white-space: pre !important;
  overflow-x: scroll !important;
  word-break: keep-all !important;
  word-wrap: initial !important;
}

aside.gdbar img{
  
  width: 80px;
  height: 80px;


}

div.centered {

  text-align: left;


}

/*
code.coffeescript {

  font-size: 5px;

}
*/

code.r{ /* Code block */
  font-size: 15px;
}

code.vhdl{

  font-size: 15px;

}
</style>

## Churn Prediction Brief 
<br>
<br>
<br>
<br>
<br>
<br>
 <center><img src="assets/img/churn_small.jpg" width="55%" height="55%" align="right"></center>

---

## What is Churn Prediction
<br>
> - # Survival Analysis
     - First used in clinical trials and the event of interest was dead or alive.

> - # Now it is widely used to predict the probability of occurrence of an event.
     - In a dataset   
        - 1: an event occurred
        - 0: an event didn't occur

---

## The purpose of Churner Prediction
<br>
> - # Find people who are likely to churn.


> - # Try to find a way to interact with them and keep them on board.

> - # Improve our retention rate.

---

## The process of Churn Prediction
<br>
> - # Choose historical data

> - # Build model based on historical data
     - Significant variables and their weight (coefficient) can be picked out.
    
> - # Apply the coefficient (weight) of variables on our current active members and get probability
> - # Pick up the first 1000 people with lowest probability


--- &twocol

## The variables we would like to look at

*** =left

>  Categorical Data  

   - Membership Status (1, 0)
   - Gender
   - Sign Up source
   - Frequency
   - Ever Saved?
   - Upgrade?
   - DD dishonoured?
   - ChargeBack

*** =right
      
>  Numeric Data  

   - Income
   - Recurring gift amount
   - Recurring first gift amount
   - Recurring last gift amount
   - Total gift amount
   - Total number of gifts
   - Age
   - Duration (Since the most recent sign up date)


---


## Model Prediction

<br>
# Choose all sign ups from the 2015-06-01 to 2015-05-31.

- Choose all the variables we would like to look at.


```
  Constituent_ID Age Gender Income Assigned_Fundraiser Sign_Up_Amount      Frequency Current_Status Status.Indicator First_Gift_Amount Last_Gift_Amount Saved Upgrade Total_Number_Of_Gifts Total_Gift_Amount DDD CCC Times DDDown
1         108859  68 Female  45000      Telefundraiser             35 MONTHLY GIVING         Active                1                25               35     0       1                    61              1145   0   0     2      0
2         121705  84 Female      0           Canvasser             25 MONTHLY GIVING     Terminated                0                25               25     0       0                     2                50   0   0     1      0
3         123157  59   Male      0      Telefundraiser             30 MONTHLY GIVING     Terminated                0                30               30     0       0                   198              2745   0   0     2      0
4          16717  83 Female  75000           Canvasser             25 MONTHLY GIVING         Active                1                 0                0     0       0                     0                 0   0   1     1      0
5          17349   0 Female  80700      Telefundraiser             50 MONTHLY GIVING         Active                1                50               50     0       0                    22               950   0   0     2      0
6          28989   0 Female  51700      Telefundraiser             26 MONTHLY GIVING         Active                1                26               26     0       0                    24               617   0   0     2      0
```


---

## Model Prediction
<br>
# Changing character/text variables into factors

> - # Values in `Current_Status`, `Gender`, `Assigned_Fundraiser` and all other categorical variables should be changed into numbers
       - `Active`, `Terminated` will become 1, 0
       - `Female`, `Male`, `Unknown` will become 1, 2, 3
       - `Telefundraiser`, `Canvasser`, `Frontliner`, `WEB`, `Saver` will become 1, 2, 3, 4, 5
<br>

> - # The numbers here are not really numeric. It only tells the programme which is first category, second category and etc. 

---

## Model Prediction
<br>
# Variable selection

--- 

## Model Prediction
<br>
# Logistic regression is used. It deals with scenarios with 2 outcomes.

<br>
Logistic regression is part of <font color="red">G</font>eneralized <font color="red">L</font>inear <font color="red">M</font>odel (`GLM`) family

The syntax of a `logistic` regression will be like the script below


```r
my_model <- glm(Status.Indicator ~ Age + Gender + Income + Assigned_Fundraier + ..., 
                data = df,
                family = binomial(logit))
```

--- &twocol

## Model Prediction

- Logistic regression gives us the coefficients of all variables that are included in the model.

*** =left


```r
my_model <- glm(Status.Indicator ~ Age 
                 + Gender 
                 + Income
                 + Assigned_Fundraiser 
                 + Sign_Up_Amount 
                 + Frequency 
                 + First_Gift_Amount 
                 + Last_Gift_Amount 
                 #+ Saved 
                 #+ Upgrade 
                 + Total_Number_Of_Gifts
                 + Total_Gift_Amount
                 + DDD 
                 + CCC 
                 + Times 
                 #+ DDDown
                 , 
                 data = df,
                 family = binomial(logit)
                )
```


*** =right


Call:
glm(formula = Status.Indicator ~ Age + Gender + Income + Assigned_Fundraiser + 
    Sign_Up_Amount + Frequency + First_Gift_Amount + Last_Gift_Amount + 
    Total_Number_Of_Gifts + Total_Gift_Amount + DDD + CCC + Times, 
    family = binomial(logit), data = df)

Deviance Residuals: 
    Min       1Q   Median       3Q      Max  
-8.4904  -0.8957   0.2722   0.8943   3.4448  

Coefficients:
                                    Estimate Std. Error z value Pr(>|z|)    
(Intercept)                        1.055e+01  1.067e+00   9.882  < 2e-16 ***
Age                                3.865e-03  1.225e-03   3.156 0.001600 ** 
GenderMale                         1.349e-01  5.048e-02   2.672 0.007535 ** 
GenderUnknown                      9.518e-02  6.871e-02   1.385 0.165986    
Income                            -2.800e-06  8.823e-07  -3.174 0.001506 ** 
Assigned_FundraiserCanvasser      -9.462e-01  8.607e-02 -10.994  < 2e-16 ***
Assigned_FundraiserFrontliner     -1.526e+00  2.094e-01  -7.287 3.18e-13 ***
Assigned_FundraiserSaver          -8.310e-01  1.055e-01  -7.878 3.33e-15 ***
Assigned_FundraiserTelefundraiser -3.263e-01  8.924e-02  -3.656 0.000256 ***
Assigned_FundraiserWEB             1.947e-01  2.129e-01   0.915 0.360415    
Sign_Up_Amount                    -2.804e-02  2.277e-03 -12.314  < 2e-16 ***
FrequencyMONTHLY GIVING           -8.854e+00  1.022e+00  -8.663  < 2e-16 ***
First_Gift_Amount                  3.618e-02  3.601e-03  10.047  < 2e-16 ***
Last_Gift_Amount                  -4.645e-02  3.783e-03 -12.279  < 2e-16 ***
Total_Number_Of_Gifts             -1.332e-02  5.724e-03  -2.328 0.019910 *  
Total_Gift_Amount                  6.330e-03  2.881e-04  21.973  < 2e-16 ***
DDD1                              -8.302e-01  9.746e-02  -8.519  < 2e-16 ***
CCC1                               1.570e-01  8.997e-02   1.745 0.080981 .  
Times                             -9.764e-01  8.628e-02 -11.316  < 2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 13916  on 10071  degrees of freedom
Residual deviance: 11248  on 10053  degrees of freedom
AIC: 11286

Number of Fisher Scoring iterations: 7

---

## How to use the output of the model
<br>

- # On the paper


- # Using R `predict()` function


```r
prob <- predict(my_model, df, type = "response")
```
   
  - # Data set `df` should contain the exact columns used in the model

  - # Please note:
       > - ahaah
       > - ahahh

---

## How do we target people
<br>

- # Apply the coefficients on current active members we can produce the probability of a person stays with us in the future.

- # We pick out people with low probability.



---

## Accuracy of Current Model
<br>
- # Currently we can pick out 16.7% of our monthly cancellations

---

## Questions?




















