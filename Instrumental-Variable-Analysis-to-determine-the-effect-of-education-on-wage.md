The effect of education on wage using Instrumental variable analysis
================
Joshua Edefo
2024-08-04

Introduction The effect of education on wage using Instrumental variable
analysis Source of endogeneity is unobserved ability to earn wage which
is correlated with education and wage. Addressing endogeneity, we are
going to use near_college as an instrument variable (IV) which meet the
criteria of IV, such as. 1. relevance ( corrected with education), 2.
exclusion (can only affect wage through education), 3. probably will not
affect the error term of the equation

Step 1: install amd Load Library

`{r a, message = FALSE} # install.packages("AER"), already installed library(AER)`

Step 2: Simulate the Data

\`\`\`{r b} set.seed(123) n \<- 1000

# Create variables

ability \<- rnorm(n) \# Unobserved ability near_college \<- rbinom(n, 1,
0.3) \# Instrumental variable educ \<- 10 + 2 \* near_college + 0.3 \*
ability + rnorm(n) \# Education level (independent variable) wage \<-
5 + 0.5 \* educ + 0.3 \* ability + rnorm(n) \# Wage with endogeneity
(dependent variable)

# Create a data frame

data \<- data.frame(wage, educ, near_college)

# Display the first few rows of the data & check the structure

head(data) str (data)


    Step 3: Explore the Data

    ```{r c}
    # Check correlations to see the strength of the instrument
    cor(data)
    # explanation of the correlations
    # near_college is correlated with edu (correct)
    # near_college not correlated with wage (correct), but can do so only through educ
    # educ and wage correlated (correct)

Step 4: Perform First-Stage Regression

\`\`\`{r d} \# Regress ‘educ’ on the instrument ‘near_college’
first_stage \<- lm(educ ~ near_college, data = data)
summary(first_stage)

# Diagnostics test

# Check for weak instruments

# The F-statistic of the first stage should be greater than 10

summary(first_stage)\$fstatistic \# explanation of F statistic \# F
statistic is 789.5 (this is greater than 10) suggesting that
near_college is a stron IV


    Step 5: Perform Second-Stage Regression

    ```{r e}
    # Regress 'wage' on the predicted values of 'educ' from the first stage
    educ_hat <- predict(first_stage, data) # data purged from endogenous variable ( ability)
    second_stage <- lm(wage ~ educ_hat, data = data)
    summary(second_stage)

    # Explanation
    #The educ_hat variable has a positive effect on the dependent variable, with a coefficient of 0.55648.
    # The model explains about 15% of the variability in the dependent variable, which is relatively modest.
    # Overall, the output suggests that the instrumental variable educ_hat has a statistically significant causative
    #impact on the dependent variable and that the model fit, is statistically robust.

Step 6: Use ivreg for Instrumental Variable Estimation

\`\`\`{r f} \# The ivreg function from the AER package performs both
stages (step 4 and 5 ) together iv_model \<- ivreg(wage ~ educ \|
near_college, data = data) summary(iv_model)


    Step 7: Interpret the Results

    ```{r g}
    # Compare OLS and IV estimates
    # OLS Regression (naive estimation ignoring endogeneity)
    ols_model <- lm(wage ~ educ, data = data)
    summary(ols_model)

    # Explanation
    #Coefficient for educ: Each additional unit of educ increases the dependent variable 
    # by approximately 0.57837 (similar for IV analysis, which is 0.55648) units. This effect is highly significant 
    # The model explains about 36.51% ( lower than IV analysis, which is 15%) of the variance in the dependent variable, 

session information

`{r i} sessionInfo()`
