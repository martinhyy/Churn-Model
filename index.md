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

- # Correlation matrix tells us some of the variable are highly correlated.

- # This means those variables can't be used in the model at the same time.

- # Which variable should be used?

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


```
                                       Estimate   Std. Error     z value      Pr(>|z|)
(Intercept)                        1.054763e+01 1.067317e+00   9.8823782  4.964059e-23
Age                                3.864882e-03 1.224649e-03   3.1559102  1.599981e-03
GenderMale                         1.349053e-01 5.048464e-02   2.6722046  7.535469e-03
GenderUnknown                      9.517646e-02 6.870871e-02   1.3852167  1.659862e-01
Income                            -2.799946e-06 8.822575e-07  -3.1736157  1.505529e-03
Assigned_FundraiserCanvasser      -9.462476e-01 8.607137e-02 -10.9937562  4.095204e-28
Assigned_FundraiserFrontliner     -1.525945e+00 2.094175e-01  -7.2866161  3.178370e-13
Assigned_FundraiserSaver          -8.310060e-01 1.054864e-01  -7.8778504  3.330612e-15
Assigned_FundraiserTelefundraiser -3.263028e-01 8.924056e-02  -3.6564409  2.557413e-04
Assigned_FundraiserWEB             1.946993e-01 2.128852e-01   0.9145739  3.604154e-01
Sign_Up_Amount                    -2.804024e-02 2.277135e-03 -12.3138275  7.631628e-35
FrequencyMONTHLY GIVING           -8.854031e+00 1.022046e+00  -8.6630496  4.593084e-18
First_Gift_Amount                  3.618227e-02 3.601388e-03  10.0467554  9.494197e-24
Last_Gift_Amount                  -4.645492e-02 3.783244e-03 -12.2791233  1.172620e-34
Total_Number_Of_Gifts             -1.332466e-02 5.723572e-03  -2.3280330  1.991035e-02
Total_Gift_Amount                  6.330043e-03 2.880891e-04  21.9725150 5.276450e-107
DDD1                              -8.301905e-01 9.745672e-02  -8.5185558  1.615551e-17
CCC1                               1.570042e-01 8.997274e-02   1.7450199  8.098141e-02
Times                             -9.763935e-01 8.628068e-02 -11.3164786  1.087532e-29
```

---

## Model Prediction

- # Variable selection


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




















