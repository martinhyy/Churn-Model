---
title: "Greenpeace Insight Skillshare"
author: "Martin Hou"
date: '22 November, 2016'
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

pre code.1c {
  font-size: 50%;

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

- # Check correlation between variables 

- # All numeric variables are checked here


```r
options(width=300)
round(cor(df[,c("Income", "Age", "Sign_Up_Amount", 
                "First_Gift_Amount", "Last_Gift_Amount", 
                "Total_Number_Of_Gifts", "Total_Gift_Amount")]),2)
```

```
                      Income   Age Sign_Up_Amount First_Gift_Amount Last_Gift_Amount Total_Number_Of_Gifts Total_Gift_Amount
Income                  1.00 -0.01           0.03              0.07             0.06                  0.06              0.09
Age                    -0.01  1.00           0.02             -0.01             0.01                  0.09              0.09
Sign_Up_Amount          0.03  0.02           1.00              0.55             0.70                  0.03              0.28
First_Gift_Amount       0.07 -0.01           0.55              1.00             0.88                  0.11              0.27
Last_Gift_Amount        0.06  0.01           0.70              0.88             1.00                  0.11              0.36
Total_Number_Of_Gifts   0.06  0.09           0.03              0.11             0.11                  1.00              0.79
Total_Gift_Amount       0.09  0.09           0.28              0.27             0.36                  0.79              1.00
```

---

## Model Prediction

- # Correlation matrix tells us some of the variable are highly correlated (collinearity ).

- # This means those variables can't be used in the model at the same time.

- # Which variable should be used?

## Model Prediction

- # A simple approach to identify collinearity among explanatory variables is the use of variance inflation factors (`VIF`)

- # The higher the value, the higher the collinearity



```r
vif <- diag(solve(cor(df[,c("Income", "Age", "Sign_Up_Amount", 
                "First_Gift_Amount", "Last_Gift_Amount", 
                "Total_Number_Of_Gifts", "Total_Gift_Amount")])))
vif
```

```
               Income                   Age        Sign_Up_Amount 
             1.011049              1.011650              2.077666 
    First_Gift_Amount      Last_Gift_Amount Total_Number_Of_Gifts 
             4.798177              6.812227              3.065939 
    Total_Gift_Amount 
             3.497294 
```

- # From the result we can tell we should firstly remove `Last_Gift_Amount` first

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
                 #+ First_Gift_Amount 
                 #+ Last_Gift_Amount 
                 #+ Saved 
                 #+ Upgrade 
                 #+ Total_Number_Of_Gifts
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


```
                                       Estimate   Std. Error     z value      Pr(>|z|)
(Intercept)                        9.862375e+00 9.018823e-01  10.9353246  7.812586e-28
Age                                3.673104e-03 1.209479e-03   3.0369299  2.390010e-03
GenderMale                         1.213508e-01 4.998496e-02   2.4277466  1.519296e-02
GenderUnknown                      7.400504e-02 6.818632e-02   1.0853355  2.777731e-01
Income                            -2.359879e-06 8.718306e-07  -2.7068094  6.793326e-03
Assigned_FundraiserCanvasser      -9.202429e-01 8.615350e-02 -10.6814343  1.243356e-26
Assigned_FundraiserFrontliner     -1.485436e+00 2.075112e-01  -7.1583422  8.165852e-13
Assigned_FundraiserSaver          -6.604016e-01 1.031417e-01  -6.4028564  1.524966e-10
Assigned_FundraiserTelefundraiser -3.445890e-01 8.926591e-02  -3.8602532  1.132696e-04
Assigned_FundraiserWEB             1.984448e-01 2.134383e-01   0.9297525  3.524992e-01
Sign_Up_Amount                    -3.655851e-02 1.977466e-03 -18.4875512  2.601006e-76
FrequencyMONTHLY GIVING           -8.114055e+00 8.641458e-01  -9.3896826  6.018135e-21
Total_Gift_Amount                  5.324844e-03 1.612737e-04  33.0174261 4.567113e-239
DDD1                              -7.822929e-01 9.595510e-02  -8.1526968  3.558972e-16
CCC1                               1.702339e-01 8.804622e-02   1.9334610  5.317944e-02
Times                             -9.527222e-01 8.066817e-02 -11.8103858  3.449746e-32
```


---

## How to use the output of the model
<br>
- # 

- # Using R `predict()` function


```r
prob <- predict(my_model, df, type = "response")
```
   
>  - # Data set `df` should contain the exact variables used in the model

>  - # Please note:
       > - If we want to use `predict()` to calculate the probability of the response variable for a given dataset, `type="response"` should be specified
       > - Otherwise, the result will be the some other value but still related to probability (log-odds)

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




















