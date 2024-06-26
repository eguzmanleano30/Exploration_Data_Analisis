## Linea regression Analysis of Customer Love for Sephora’s products 

### Research question 

Which factors influence the level of customer love for products sold on Sephora’s website? 

### Explanation of research question 

This research question investigates the factors contributing to the level of customer love for products available on Sephora’s website. The dependent variable, “love,” represents the number of people who love a particular product, indicating customer satisfaction and affinity towards the product. 

### Building a linear regression model for people who love products of Sephora beauty retail 

We will use a multiple linear regression model to examine what pushes customer love for Sephora's products. This approach allows us to assess how price, brand, marketing efforts, and customer reviews affect the love variable, which measures customer affection toward products. To analyze these variables together, the model will highlight the most significant influences of customer love, offering insights into practical strategies for enhancing product appeal on Sephora's platform. 

**Getting a sample of Sephora's data**
```{r}
# load data 
sephora <- read.csv("./data/cosmetic/SecondSephoraClean.csv", header = TRUE)

set.seed(123)  # Set a seed for reproducibility

# Sample 5000 rows from ds_wfood_cols_us_nona
sephoraData <- sample_n(sephora, 1000, replace = FALSE)
```

### Correlation Analysis: The log love variable in Sephora data 

Analyzing the log love correlations in the Sephora dataset highlights a few trends: 

-  **Strong Positive Correlation with log number of reviews:** A robust positive correlation (0.84) with log number of reviews indicates that products with more reviews are generally more loved, suggesting the importance of customer feedback in driving affection towards products. 

-  **Negative Correlation with Online Only:** The negative correlation (-0.38) with online-only suggests that products exclusively sold online are less loved, possibly reflecting a preference for in-store experiences. 

-  **Slight Negative Correlation with log price:** log love shows a slight negative correlation with both log_price (-0.18) and log_value_price (-0.20), indicating that higher-priced products may be less accessible or have higher expectations that are not always met. 

 -  **Positive Correlation with rating:** A moderate positive correlation (0.34) with rating suggests that better-rated products are more loved, highlighting the value of product quality and satisfaction. 

**Correlation Matrix of Sephora data with all predictors**  

```{r}
# Selecting all predictors
totalVariable <- select(sephoraData, "log_love", "log_number_of_reviews", "log_price",
                   "log_value_price", "rating", "online_only", "exclusive", "limited_edition", "limited_time_offer")

# argument lab = TRUE
ggcorrplot(round(cor(totalVariable), 3),
           hc.order = TRUE,
           type = "lower",
           lab = TRUE)
```

![image](https://github.com/eguzmanleano30/Business_Analysis/assets/172155030/905c9159-638f-4f66-be8d-82f14f22bce6)

```{r}
# Calculate the correlation matrix
correlation_matrix <- cor(sephoraNum)

# Get the row and column indices for the lower triangle
lower_triangle_indices <- which(lower.tri(correlation_matrix), arr.ind = TRUE)

# Extract the lower triangle of the correlation matrix along with variable names
lower_triangle <- correlation_matrix[lower_triangle_indices]

# Get the variable names corresponding to the lower triangle
row_names <- rownames(correlation_matrix)[lower_triangle_indices[, 1]]
col_names <- colnames(correlation_matrix)[lower_triangle_indices[, 2]]

# Create a data frame to display the lower triangle with variable names
lower_triangle_df <- data.frame(Variable1 = row_names,
                                Variable2 = col_names,
                                Correlation = lower_triangle)

# Sort the lower triangle data frame by variable 1 in ascending order
sorted_lower_triangle_df <- lower_triangle_df[order(lower_triangle_df$Variable2), ]

# table the correlation
kbl(sorted_lower_triangle_df) %>%
kable_classic_2(full_width = F)
```



 
**Log love response correlation with all predictors** 

| Variable 1             | Variable 2             | Correlation |
| --------------------- | --------------------- | ----------- |
|   | Log price             | \-0.18      |
|   | Log value price       | \-0.2       |
| **Log love**  | Log number of reviews | 0.84        |
|   | rating                | 0.34        |
|   | Online only sales     | \-0.38      |
|   | Exclusive             | 0.12        |
|   | Limited edition       | \-0.11      |


### L.I.N.E.  assumption diagnostic for numerical variables 

### Diagnostic of linearity and independence assumptions 

The linearity assumption test results for multiple linear regression of Sephora data suggest no evident pattern observed in the residuals versus predictors and residuals versus fitted values plots for the log value price, log price, and rating predictors, indicating no violation of linearity assumptions. However, for the log number of reviews predictor, a clear U-shaped pattern is observable in both plots, indicating a violation of the linearity assumption. 

On the other hand, examining the independence assumption revealed consistent outcomes across predictors. Each predictor, including log value price, log price, log number of reviews, and rating, exhibited random cloud patterns in the residuals versus order plots. This randomness suggests no systematic relationship between residuals and the order of observations. Therefore, it’s indicated no violation of the independence assumption for any of the predictors. 

**Results of diagnostics of linearity and independence assumptions**
|   | Linearity assumption  |   |   | Independence assumption  |   |   |  
| --------------------- | --------------------- | -------------------------- | ---------------- | ------------------ | ------------ | --------------- |
|  **<br>Predictor**        | **Residual vs predictor** | **Residual vs. fitted values** | **Conclusion**       | **Residual vs. order** | **Conclusion**   | **Output and code** |
| Log value price       | No pattern            | No pattern                 | No <br>Violation | Random cloud       | No violation | [Appendix](Linear_Regression/Value_price_lineari_Independ.pdf)        |
| Log price             | No pattern            | No pattern                 | No <br>Violation | Random cloud       | No violation | [Appendix](Linear_Regression/Price_lineari_Independ.pdf)        |
| Log number of reviews | U pattern             | U pattern                  | Violation        | Random cloud       | No violation | [Appendix](Linear_Regression/number_review_lineari_Independ.pdf)        |
| Rating                | No pattern            | No pattern                 | No <br>Violation | Random cloud       | No violation | [Appendix](Linear_Regression/Rating_lineari_Independ.pdf)        |


### Diagnostics of normality assumptions for predictors 

Based on the analysis of the normality assumption – as illustrated in the following table -- for the numerical variables in Sephora data, it can be concluded that most predictors satisfy the normality assumption. Indeed, log value price, and log price rating variables exhibit straight diagonal lines in their QQ plots, indicating conformity to a normal distribution. Correspondingly, their histograms display a normal distribution pattern, with some outliers on the left side of the boxplot. The Shapiro-Wilks tests for these predictors failed to reject the null hypothesis, with p-values of 0.243, 0.224, and 0.204, confirming that errors are normally distributed. Therefore, there is no violation of the normality assumption. However, for the log number of reviews, the QQ plot shows an outlier down the line and slight skewness to the right in the histogram, with some outliers mainly on the right side of the boxplot. The Shapiro-Wilks test rejected the null hypothesis with a very low p-value, indicating that errors are not normally distributed. Therefore, this is a violation of the assumption of normality.

**Result of diagnostics of normality assumption**

| Predictor              | QQ plot                 | Histogram                     | Boxplot                              | Shapiro-Wilks test                                               | Conclusion     | Output and code |
|------------------------|-------------------------|-------------------------------|--------------------------------------|------------------------------------------------------------------|----------------|-----------------|
| Log value price        | straight diagonal line  | Norm. Ditribu.                | some outliers left                   | Fail reject Ho  p-value: 0.243  Errors are normally distributed  | No. violation  | [Appendix](Linear_Regression/Normality_value_price.pdf)        |
| Log price              | straight diagonal line  | Norm. Ditribu.                | some outliers left                   | Fail reject Ho  p-value: 0.224  Errors are normally distributed  | No. violation  | [Appendix](Linear_Regression/Normality_price.pdf)        |
| Log number of reviews  | Outlier down line       | slight skewness to the right  | some outliers right                  | reject Ho  p-value=1.58e-9  Errors are NOT normally distributed  | violation      | [Appendix](Linear_Regression/Normality_numb_of_review.pdf)        |
| Rating                 | straight diagonal line  | Norm. Ditribu.                | some outliers to the left and right  | Fail reject Hop-value: 0.204  Errors are normally distributed    | No. violation  | [Appendix](Linear_Regression/Normality_rating.pdf)        |



### Diagnostics of equal variance assumption for predictors 

The equal variance assumption for the numerical variables – as shown in the following table -- in the Sephora dataset presents mixed results. The scatter plots show random clouds for comparisons against predictors and residuals for log value price. Levene’s Test results in a failure to reject the null hypothesis with high p-values (0.789 and 0.457), indicating constant error variance and no assumption violation. However, the assumption encounters issues with the log number of reviews and ratings. Both variables show U-shaped patterns in their residual plots, suggesting non-constant variance across values. This observation is confirmed by Levene’s Test, which rejects the null hypothesis for both (p-values of 0.002 and 5.379e-06, respectively), indicating a clear violation of the equal variance assumption for these predictors. 

**Result of diagnostic of equal variance assumption**

|   Predictor            | Log love vs Predictor  | Residual vs predictor  | Levene’s Test                                                | Conclusion    | Output and code |
|------------------------|------------------------|------------------------|--------------------------------------------------------------|---------------|-----------------|
| Log value price        | Random cloud           | Random cloud           | Fail reject Ho  P-value: 0.789  Error variance is constant   | No violation  | [Appendix](Linear_Regression/equal_variance_value_price.pdf)        |
| Log price        | Random cloud           | Random cloud           | Fail reject Ho  P-value: 0.457  Error variance is constant   | No violation  | [Appendix](Linear_Regression/equal_variance_price.pdf)        |
| Log number of reviews  | Random cloud           | U shape                | reject Ho  P-value: 0.002  Error variance is constant        | Violation     | [Appendix](Linear_Regression/equal_variance_numb_of_review.pdf)        |
| Rating                 | U shape                | U shape                | reject HoP-value: 5.379e-06  Error variance is not constant  | Violation     | [Appendix](Linear_Regression/equal_variance_rating.pdf)        |


### Analysis of categorical variables 

The t-test analysis for the influence of categorical variables on Sephora data demonstrates significant results for most variables, suggesting they should not be excluded from the linear regression model. The predictors "Online only," "Exclusive," and "Limited edition" give t-values (12.97, 3.38, and 3.52, respectively) that significantly exceed the critical value of 1.65. Their p-values indicate a solid rejection of the null hypothesis (all p-values are 0). This suggests that these variables have a statistically significant impact on the dependent variable and should be included in the model. Contrarily, the "Limited time offer" variable, with a t-value of 0.38 and a p-value of 0.703, fails to reject the null hypothesis, suggesting it does not significantly affect the dependent variable. Therefore, it can be excluded from further modeling. 

**T-test result for categorical variables**

| Predictor           | T*    | T    | p-value  | T-test                                 | Conclusion           | Output and code |
|---------------------|-------|------|----------|----------------------------------------|----------------------|-----------------|
| Online only         | 12.97 | 1.65 | 0        | Reject Ho  Statistic significant       | No exclude variable  | [Appendix](Linear_Regression/Analisis_of_online_only_variable.pdf)        |
| exclusive           | 3.38  | 1.65 | 0        | Reject Ho  Statistic significant       | No exclude variable  | [Appendix](Linear_Regression/Analisis_of_exclusive_variable.pdf)        |
| Limited edition     | 352   | 1.65 | 0        | Reject Ho  Statistic significant       | No exclude variable  | [Appendix](Linear_Regression/Analisis_of_limited_edition_variable.pdf)        |
| Limited time offer  | 0.38  | 1.65 | 0.703    | Fail Reject Ho  Statistic significant  | exclude variable     | [Appendix](Linear_Regression/Analisis_of_limited_time_offer.pdf)        |


### Predictors selected to build linear regression model

The selection of six predictors for the linear regression model analyzing log love in Sephora data —rating, number of reviews, log price, log value price, exclusive, and limited edition— is based on their statistical significance and relevance, as supported by LINE assumption analysis for numerical variables, and the t-test for categorical variables. 


```{r}
# Select numerical variables 
variablesSelecting <- select(sephoraData, "rating", "log_number_of_reviews", "log_price", "log_value_price", "exclusive", "limited_edition","log_love")


# Create pairs plot using ggpairs
ggpairs(variablesSelecting)
```
**Correlation with all predictors selected**

![image](https://github.com/eguzmanleano30/Business_Analysis/assets/172155030/78d90195-bca6-43ee-95c8-0acdb2f05371)


**Preliminary model:** 

```{r}
# Fit linear regression model
preli_model2 <- lm(log_love ~ rating + log_number_of_reviews + log_price + log_value_price + factor(exclusive) + factor(limited_edition), data = sephoraData)

sum_preliminar.model <- tidy(preli_model2)

kbl(sum_preliminar.model) %>%
kable_classic_2(full_width = F)
```

**log(love)** = 5.871+ 0.088*rating + 0.684*log(number_of_reviews) - 0.806*Log(price) +   0.659*Log(value_price) +0.309*exclusive + 0.283*limited_edition 


**Summary of preliminary model**

```{r}
# Fit linear regression model
preli_model2 <- lm(log_love ~ rating + log_number_of_reviews + log_price + log_value_price + factor(exclusive) + factor(limited_edition), data = sephoraData)

sum_preliminar.model <- tidy(preli_model2)

kbl(sum_preliminar.model) %>%
kable_classic_2(full_width = F)
```

|                        | Coefficient  | SE     | z       | p.value  |
|------------------------|--------------|--------|---------|----------|
| (Intercept)            | 5.871        | 0.182  | 32.310  | 0.000    |
| rating                 | 0.088        | 0.026  | 3.361   | 0.001    |
| log_number_of_reviews  | 0.684        | 0.015  | 45.563  | 0.000    |
| log_price              | -0.806       | 0.289  | -2.793  | 0.005    |
| log_value_price        | 0.659        | 0.284  | 2.320   | 0.021    |
| exclusive              | 0.309        | 0.061  | 5.102   | 0.000    |
| limited_edition        | 0.283        | 0.103  | 2.747   | 0.006    |


### Model refinement and selection 

The analysis of the Sephora data using linear regression explored various models to determine which factors most significantly influence customer affection, as measured by the love variable. Two criteria, $R^2_a$ (adjusted R-squared) and $C_p$ (Mallow's Cp), were employed to assess model performance. Among the models tested, model six was identified as optimal according to both criteria. This model incorporates six predictors: rating, log number of reviews, log price, log value price, exclusivity, and limited edition. It achieved an adjusted R-squared of 0.730 and a Cp value of 7, suggesting it has substantial explanatory power while effectively balancing complexity and fit. This comprehensive approach ensures that the selected model provides a robust framework for understanding the dynamics of customer preferences on Sephora’s platform.

**Result of examination of all best models using $R^2_a$ criterion**  

```{r}
# Create a design matrix with variables VA1, VA7, and VA10
x <- sephoraData[, c("rating", "log_number_of_reviews", "log_price", "log_value_price", "exclusive", "limited_edition")]

# Perform stepwise variable selection using leaps
Ra2 <- leaps(x = x, y = sephoraData$log_love, method = "adjr2", nbest = 1)

Ra2_table <- data.frame(Ra2$which, num_in_model = Ra2$size - 1, 
                        p = Ra2$size,
                        adjr2 = Ra2$adjr2) %>% 
  arrange(desc(adjr2))

kbl(Ra2_table) %>%
kable_classic_2(full_width = F)
```


| Num of model  | X1     | X2    | X3     | X4     | X5     | X6     | Number of predictors  | adjr2  |
|---------------|--------|-------|--------|--------|--------|--------|-----------------------|--------|
| 6             | TRUE   | TRUE  | TRUE   | TRUE   | TRUE   | TRUE   | 7                     | 0.730  |
| 5             | TRUE   | TRUE  | TRUE   | FALSE  | TRUE   | TRUE   | 6                     | 0.729  |
| 4             | FALSE  | TRUE  | TRUE   | FALSE  | TRUE   | TRUE   | 5                     | 0.726  |
| 3             | FALSE  | TRUE  | FALSE  | FALSE  | TRUE   | TRUE   | 4                     | 0.723  |
| 2             | FALSE  | TRUE  | FALSE  | FALSE  | TRUE   | FALSE  | 3                     | 0.72   |
| 1             | FALSE  | TRUE  | FALSE  | FALSE  | FALSE  | FALSE  | 2                     | 0.707  |



**Result of examination of all best models using $C_p$ criterion** 

```{r}
Cp <- leaps(x = x, y = sephoraData$log_love,
             method = "Cp", nbest = 1)

#re-organize results and sort by lowest Cp value
Cp_table <- data.frame(Cp$which, 
                       num_in_model = Cp$size - 1,
                       p = Cp$size,
                       Cp = Cp$Cp) %>% 
  arrange(Cp)

kbl(Cp_table) %>%
kable_classic_2(full_width = F)
```

| Num of model  | X1     | X2    | X3     | X4     | X5     | X6     | Number of predictors  | Cp     |
|---------------|--------|-------|--------|--------|--------|--------|-----------------------|--------|
| 6             | TRUE   | TRUE  | TRUE   | TRUE   | TRUE   | TRUE   | 7                     | 7.00   |
| 5             | TRUE   | TRUE  | TRUE   | FALSE  | TRUE   | TRUE   | 6                     | 10.38  |
| 4             | FALSE  | TRUE  | TRUE   | FALSE  | TRUE   | TRUE   | 5                     | 19.73  |
| 3             | FALSE  | TRUE  | FALSE  | FALSE  | TRUE   | TRUE   | 4                     | 30.25  |
| 2             | FALSE  | TRUE  | FALSE  | FALSE  | TRUE   | FALSE  | 3                     | 42.24  |
| 1             | FALSE  | TRUE  | FALSE  | FALSE  | FALSE  | FALSE  | 2                     | 88.89  |


**Result of examination of all best models using $C_p$ criterion**

```{r}
regfit.full <- regsubsets(log_love ~ rating + log_number_of_reviews + log_price + log_value_price + exclusive + limited_edition , data = sephoraData)

plot(regfit.full, scale = "Cp")
```

![image](https://github.com/eguzmanleano30/Business_Analysis/assets/172155030/4716ee19-9ff7-40f7-bb8b-e30a1adebf59)


**Preliminary final model:**

```{r}
sephoraData <- sephoraData %>% 
  mutate(exclusive = factor(exclusive))

sephoraData <- sephoraData %>% 
  mutate(limited_edition = factor(limited_edition))

# Fit linear regression model
preli_model3 <- lm(log_love ~ rating + log_number_of_reviews + log_price + log_value_price + exclusive + limited_edition, data = sephoraData)

sum_preliminar.model3 <- tidy(preli_model3)
```

**log(love)** = 5.871+0.088*rating + 0.684*log(number_of_reviews) +-0.806*Log(price) + + 0.659*Log(value_price)+0.309*exclusive + 0.283*limited_edition 



**Relationship between log love response variable and its predictors** 

```{r}
avPlots(preli_model3)
```

![image](https://github.com/eguzmanleano30/Business_Analysis/assets/172155030/d6bc4c21-70b4-4038-b2db-03d94397c7ae)


### Interaction analysis 

Based on the results of the interaction analysis using an F-test with α = 0.01, it can be concluded that there is no statistically significant interaction between the variables examined. The study compared various interactions, such as limited edition status versus rating, limited edition versus log number of reviews, etc. In each case, the F-test did not generate a significant result, failing to reject the null hypothesis of no interaction. This suggests that the relationship between the variables remains consistent across different levels of the interacting factors. Therefore, these findings indicate that the linear regression model built for log love response does not adequately require additional interaction terms to explain the variation in customer affection. 

**Result of interaction analysis**

|                                           |                    | **F test with α = 0.01**  |                   |          |                                         |                    |                 |
|-------------------------------------------|--------------------|-----------------------|-------------------|----------|-----------------------------------------|--------------------|-----------------|
|   **Interaction**                             |   **Plot regression**  | **F***                    | **F (0.99, 1, 996)**  | **P-value**  | **Result of test**                          |   **Conclusion**       | **Output and code** |
| limited edition vs rating                 | Parallel lines     | 4.46                  | 6.66              | 0.037    | Fail reject Ho  Not statist. Signific.  | No interaction     | [Appendix](Linear_Regression/Interac_limited_edition_vs_value_price.pdf)        |
| limited edition vs log number of reviews  | Parallel lines     | 0.324                 | 6.66              | 0.569    | Fail reject Ho  Not statist. Signific   | No interaction     | [Appendix](Linear_Regression/Interac_limited_edition_vs_numb_review.pdf)        |
| limited edition vs. log price             | Parallel lines     | 2.224                 | 6.66              | 0.136    | Fail reject Ho  Not statist. Signific   | Not interaction    | [Appendix](Linear_Regression/Interac_limited_edition_vs_price.pdf)        |
| limited edition vs. log value price       | Parallel lines     | 2.178                 | 6.66              | 0.14     | Fail reject Ho  Not statist. Signific   | Not interaction    | [Appendix](Linear_Regression/Interac_limited_edition_vs_rating.pdf)        |
| exclusive vs. rating                      | Parallel lines     | 0.124                 | 6.66              | 0.725    | Fail reject Ho  Not statist. Signific   | Not interaction    | [Appendix](Linear_Regression/Interac_exclusive_vs_rating.pdf)        |
| exclusive vs. log number of reviews       | Parallel lines     | 0.638                 | 6.66              | 0.425    | Fail reject Ho  Not statist. Signific   | Not interaction    | [Appendix](Linear_Regression/Interac_exclusive_vs_numb_review.pdf)        |
| Exclusive vs. log value price             | Parallel lines     | 4.345                 | 6.66              | 0.037    | Fail reject Ho  Not statist. Signific   | No interaction     | [Appendix](Linear_Regression/Interac_exclusive_vs_value_price.pdf)        |
| Exclusive vs. log price                   | Parallel lines     | 5.411                 | 6.66              | 0.02     | Fail reject Ho  Not statist. Signific   | No interaction     | [Appendix](Linear_Regression/Interac_exclusive_vs_price.pdf)        |



### Model Validation 

### _Model validation with independent data_ 

The validation of the linear regression model, utilizing both training and testing data derived from the Sephora dataset, reveals the model's reliability and predictive performance. The review of the coefficients demonstrates consistency across both datasets, with factors such as rating, the logarithm of the number of reviews, and the perceived value price displaying statistically significant impacts on customer affection. Additionally, variables like log price and exclusive and limited edition affect product love in both sets. Despite a slightly higher Mean Squared Prediction Error (MSPE) for the testing data than the Mean Squared Error (MSE) for the training data, their proximity suggests that the model's predictive ability remains unbiased and reasonably accurate. Overall, the validation process underscores the reliability of the linear regression model in predicting customer affection for Sephora products. 

**Statistic summary of model with training data**

```{r}
training <- sephoraData

full <- lm(log_love ~ rating + log_number_of_reviews + log_price + log_value_price + exclusive + limited_edition, data = training)

base <- lm(log_love ~ 1, data = training)

m1_training <- stats::step(full,
scope = list(upper= full,
lower= ~ 1),
direction = "both", trace = FALSE)


summary.m1_training <- summary(m1_training)

kbl(tidy(m1_training)) %>%
kable_classic_2(full_width = F)
```

|                       | Coefficient | SE    | T value | p\-value |
| --------------------- | ----------- | ----- | ------- | -------- |
| (Intercept)           | 5.871       | 0.182 | 32.31   | 0        |
| rating                | 0.089       | 0.026 | 3.361   | 0        |
| log_number_of_reviews | 0.684       | 0.015 | 45.56   | 0        |
| log_price             | \-0.806     | 0.289 | \-2.793 | 0.005    |
| log_value_price       | 0.658       | 0.284 | 2.320   | 0.021    |
| exclusive1            | 0.309       | 0.061 | 5.102   | 0        |
| limited_edition1      | 0.283       | 0.103 | 2.747   | 0.006    |


**Statistic summary of model with trining data**

```{r}
first_validation <- read.csv("./data/cosmetic/SecondSephoraClean.csv", header = TRUE)


# Get the IDs from the first sample
ids_in_training <- training$id

# Exclude rows with these IDs from the second dataset before sampling again
sephora_remaining <- anti_join(first_validation, training, by = "id")

# Now, sample 2000 (or any other number you wish) rows from the remaining data
set.seed(036)  # Change the seed for a different sample
testing <- sample_n(sephora_remaining, 2000, replace = FALSE)

training <- training[, -1]

testing <- testing %>%
  select(id, log_love, rating, log_number_of_reviews, log_price, log_value_price, exclusive, limited_edition)

# Encoding type of product as factor
testing$exclusive = as.factor(testing$exclusive)

testing$limited_edition = as.factor(testing$limited_edition)

# Fit the same model as m1_training using the same formula
m1_testing <- lm(log_love ~ rating + log_number_of_reviews + log_price + log_value_price + exclusive + limited_edition, data = testing)

kbl(tidy(m1_testing)) %>%
kable_classic_2(full_width = F)
```

|                       | Coefficient | SE    | T value | p\-value |
| --------------------- | ----------- | ----- | ------- | -------- |
| (Intercept)           | 5.821       | 0.139 | 41.945  | 0        |
| rating                | 0.139       | 0.022 | 6.431   | 0        |
| log_number_of_reviews | 0.683       | 0.012 | 59.294  | 0        |
| log_price             | \-1.156     | 0.215 | \-5.391 | 0        |
| log_value_price       | 0.97        | 0.211 | 4.596   | 0        |
| exclusive1            | 0.117       | 0.046 | 2.55    | 0.011    |
| limited_edition1      | 0.371       | 0.071 | 5.263   | 0        |



```{r}
pred <- predict(m1_training, testing)

MSPE <- round(mean((testing$log_love - pred)^2),3)
```
![image](https://github.com/eguzmanleano30/Business_Analysis/assets/172155030/d29a7bbd-33e3-4ecb-865e-b39db2b47f8e)


### Cross-validation when no independent data available 

The average Mean Squared Prediction Error (MSPE) and Root Mean Squared Error (RMSE) across multiple folds indicate the model's consistency and effectiveness in capturing the underlying relationships within the Sephora dataset. Despite variations in the MSPE and RMSE values between folds, the average metrics remain relatively stable, suggesting that the model's predictive ability is robust and reliable across different subsets of the data. 

```{r}
data <- rbind(training, testing)
n <- dim(data)[1]
num.folds <- 10

#set random seed so results are reproducible
set.seed(100)

#randomly assign each observation to a "fold" (1-10)
folds <- sample(rep(1:num.folds, length = n))

#initialize an object to store MSPE values
MSPE <- rep(0, num.folds)


#for each fold, split data into training & testing
#fit model from training
#obtain predicted values from testing
#compute MSPE
for (k in 1:num.folds){
training <- data[folds!=k, ]
testing <- data[folds==k, ]
Candidate.model <- lm(m1_training, data = training)
pred <- predict(Candidate.model, testing)
MSPE[k] <- mean((testing$log_love - pred)^2)
}
c(avg_MSPE = mean(MSPE), avg_RMSE = mean(sqrt(MSPE)))
```

**Average MSPE   = 0.721    average RMSE = 0.848**

```{r}
# Initialize an object to store MSPE values
MSPE <- rep(0, num.folds)

# For each fold, split data into training & testing,
# fit model from training, obtain predicted values from testing, and compute MSPE
for (k in 1:num.folds) {
  # Get indices of observations not used in the previous process
  excluded_indices <- which(folds != k)
  
  # Split data into folds for this iteration
  testing_data <- data[excluded_indices, ]
  training_data <- data[-excluded_indices, ]
  
  # Fit model from training data
  Candidate.model <- lm(m1_training, data = training_data)
  
  # Obtain predicted values from testing data
  pred <- predict(Candidate.model, newdata = testing_data)
  
  # Compute MSPE
  MSPE[k] <- mean((testing_data$log_love - pred)^2)
}

# Calculate and print the average MSPE and RMSE
c(avg_MSPE = mean(MSPE), avg_RMSE = mean(sqrt(MSPE)))
```

**Average MSPE   = 0.737    average RMSE = 0.859**

### Model Diagnostics 

**_Outlying Y observations_**

Some observations of log love response are atypical, but they are very few. 

```{r}
## Fit 2 variable model
m_X1X2 <- lm(log_love ~ rating + log_number_of_reviews + log_price + log_value_price + exclusive + limited_edition, data = sephoraData) 


#from olsrr package
ols_plot_resid_stand(m_X1X2)  # plot studentized residuals
```

![image](https://github.com/eguzmanleano30/Business_Analysis/assets/172155030/7e5446a4-5d6d-4366-9c1f-c2ee9132e4bd)


```{r}
ols_plot_resid_stud(m_X1X2)  # plot studentized deleted residuals
```

![image](https://github.com/eguzmanleano30/Business_Analysis/assets/172155030/be4284d5-3136-4595-8c37-cdc4dddbf262)

```{r}
ols_plot_resid_stud_fit(m_X1X2)  # plot deleted vs predicted
```

![image](https://github.com/eguzmanleano30/Business_Analysis/assets/172155030/8a28d9a5-4883-4e4c-82c4-52cc1bd0945b)

