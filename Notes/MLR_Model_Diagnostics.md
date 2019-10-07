# Multiple Linear Regression: [Model Diagnostics](https://www2.stat.duke.edu/courses/Fall19/sta210.001/slides/lec-slides/11-model-diagnostics.pdf)
Lecture Date: 10.02.19\
Review Date: 10.07.19\
\# of Slides: 51

### Dataset
#### Resaurant tips
- Reponse:
  - Tip: amount of the tip
- Predictors:
  - Party: **number of people** in the party
  - Meal: time of day (Lunch, Dinner, Late Night)
  - Age: age **category** of person paying the bill (Yadult, Middle, SenCit) 

### Table of contents
- Nested F Test [5]
- Model Diagnostics
  - Influential and Leverage Points
  - Standardized Residuals
  - Cook's Distance
- Multicollinearity

## Nested F Tests
#### Is **Meal** a significant predictor of tips? [7]
How is this different from what we've done before?\
Meal is categorized -> MealLate Night, MealLunch, (MealDinner is the baseline)\
Naively, there are two p values, one for MealLate Night (0.000), and the other for MealLunch (0.130)\
One is significant, and the other is not -> suggesting that we should do something else to check significance

NH: Beta_latenight = Beta_lunch = 0
AH: at least one Beta_i is not equal to 0
(again, when there are levels, the AH is usually "at least one is not equal to 0")

```R
reduced <- lm(Tip ~ Party + Age, data = tips) # first linear model without Meal is one of the predictors
full <- lm(Tip ~ Party + Age + Meal, data = tips) # second linear model including Meal
kable(anova(reduced, full), format="markdown", digits = 3)
```

Response:\
At least one coefficient associated with Meal is not zero. Therefore, Meal is a significant predictor of Tips.


#### Are there any significant interaction effects with Party in the model?
```R
reduced <- lm(Tip ~ Party + Age + Meal, data = tips)
full <- lm(Tip ~ Party + Age + Meal + Age* Party + Meal * Party, data = tips)
kable(anova(reduced, full), format="markdown", digits = 3)
```
Reponse:\
We conclude that there are no significant interactions with Party in the
model. Therefore, we will use the original model that only included
main effects.

## Model Diagnostics
### Influential and Leverage Points
An observation is influential if removing it substantially changes the coefficient of the regression model.
- can impact coefficient and standard errors
- quantify: 
  - leverage
  - standardized residuals
  - Cook's distance

### Leverage
measure of the distance btween an observation's values of the predictor variables and the average values of the predictor variables for the whole data set

An observation has high leverage if its combination of values for the predictor variables is very far from the typical combinations in the data

**Identifying points with high leverage has nothign to do with the values fo the response variables.**
Only looking at the predictor variables, ie the x's

##### High Leverage
- values between 1/n and 1 (for each observation)
- threshold for high leverage: h_i > 2(p+1)/n
- observations with high leverage tend to have small residuals (model tends to please those points)

Questions to check if you identify points with high leverage:
- data entry errors?
- in the scope for the individuals you want to make a prediction for?
- impact estimates of coefficients, especially interactions?

**Having high leverage does not necessarily means it would have a substantial impact on the regression -> check other mesaures"
Why? Becuase the response variable is not taken into consideration when calcualting leverage. A point can be far away from other points but still be on the same regression line.

### Standardized and Studentized Residuals
Identify outliers?\
Find points with large residuals\
Should use common scale -> residual divided by its standard error

#### Standardized Residuals
Standard error of a residual: sigma*sqrt(1-h_i)
Residuals with:
high leverage -> small variance\
low leverage -> large variance\
Why? regression tries to git high leverage observations closely

What are outliers?\
Values with large standardized residuals -> don't fit the model too well

Threshold?\
observations with standardized residual > 2 should be examined more closely

**Outliers may not have an impact on the regression line**

Good practice: Make residual plots with standardized residuals
- It is easier to identify outliers and check for constant variance assumption


#### Cook's Distance
measure of an observation's overall impact -> what will happen to the estimated coefficients if this observation is removed?

