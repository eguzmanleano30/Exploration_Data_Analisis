# Exploration Data Analsis of Sephora website

Sephora website data incorporates varied variables, capturing product characteristics and customer interactions on Sephora's website. Categorical variables include product brand, category, size, marketing flags, and product details, while numerical variables comprise product ID, rating, number of reviews, customer affection (love), and price-related metrics. Before analysis, the dataset undergoes meticulous cleaning to handle missing values, rectify data errors, and ensure uniformity across categories. The variables irrelevant to our research questions will be eliminated to streamline the analysis. 


## Understand The Dataset
The data was obtained from Kaggle that is an online community and platform for data scientists and machine learning practitioners to find and publish datasets, explore and build models, and compete in data science challenges. This dataset is valuable for projects involving market analysis, consumer behavior studies, and trend analysis in the beauty industry. https://www.kaggle.com/datasets/raghadalharbi/all-products-available-on-sephora-website/data

Sephora's dataset – with 21 variables and 91,658 observations – provides essential insights into its product offerings online. This information discloses valuable insights into Sephora's product lineup, customer preferences, and marketing strategies. Sephora's variables are grouped into four types of variables:

-   **Informative Variables:** These include essential product details, such as their IDs, brands, names, sizes, URLs, available options (like colors and sizes), product details, usage instructions, ingredients, and marketing flags. 

-   **Binary Variables:** These variables indicate specific product attributes or conditions using binary values, such as whether the product is sold exclusively online, is exclusive to Sephora's website, is a limited-edition item, or has a limited-time offer

-   **Categorical Variables:** This category encompasses the types or categories to which products belong on the Sephora website, providing a structured way to organize and classify products.

-   **Numerical Variables:** These variables quantify different aspects of the products, including their ratings, the number of reviews they have received, the number of customers who have shown interest or "love" for the products, their prices, and their value prices (applicable for discounted products).

```{r}
dim(sephora)

skim(sephora)
```
[ouput](EDA/picture/ExplorationData.png)

## Clean the Data 

### _Addressing Inconsistent Rows._  

During the data examination process, it was noted that 58 rows exhibited inconsistencies or errors. These rows were excluded from the data to uphold data integrity and reliability. 

### _Addressing Missing Values._ 

The Sephora dataset reveals 5082 significant missing values in two specific variables (rating and MarketingFlags_content), highlighting potential gaps in the dataset's completeness and accuracy. These missing entries underline the importance of addressing data integrity and the need for careful preprocessing before any analytical or predictive modeling efforts. 

**a)  Missing Values in rating:** The rating variable has 371 missing values. Given the importance of the rating variable in our analysis, we decided to retain these missing values. Imputation methods were not applied due to the relatively small proportion of missing values. 

**b)  Exclusion of MarketingFlags_content:** The MarketingFlags_content variable had many missing values, with 4711 observations missing. Considering its limited relevance to our analysis objectives and the high proportion of missing values, we excluded this variable from the entire dataset. 

```{r}
md.pattern(sephora[c("category", "number_of_reviews", "love", "price","value_price", "MarketingFlags", "online_only", "exclusive", "limited_edition", "limited_time_offer", "log_love", "log_price", "log_value_price", "log_number_of_reviews", "rating", "MarketingFlags_content")], rot = 45)
```

**Pattern of missingness between variables in the Sephora data set**
![MissingValues](https://github.com/eguzmanleano30/Exploration_Data_Analisis/assets/172155030/5a49fd44-792e-403f-bb60-360ed5804c0d)


### Exploration of numerical variables.  

The following table shows the result of the exploration analysis of numerical variables in the Sephora dataset. It reveals consistent right-skewed distributions across the “love,” “number of reviews,” “price,” and “value price” variables, with skewness ranging from 3.13 to 9.75 and kurtosis from 17.56 to 162.13. Visual examination through QQ plots, box plots, and histograms confirms the presence of outliers, particularly to the left. Appendices B, C, D, and E provide detailed exploration results for each variable mentioned. Given the pronounced skewness and outliers, logarithmic transformation is recommended to normalize the data and improve its distributional characteristics, facilitating a more robust analysis. The appendixes from Appendix B to Appendix E illustrates the exploration analysis in more details for each variable. 





| Variable          | Skewness | Kurtosis | QQ Plot      | Box Plot                  | Histogram           | Transfor. suggestion | Output and code   |
| ----------------- | -------- | -------- | ------------ | ------------------------- | ------------------- | -------------------- |------------------ |
| love              | 9.63     | 162.13   | Inverted "L” | Many outliers to the left | Skewed to the right | Logarithmic          | [Appendice B](EDA/PDF/exploration_love_variable.pdf)   |
| Number of reviews | 9.75     | 135.21   | Inverted "L” | Many outliers to the left | Skewed to the right | Logarithmic          | [Appendice C](EDA/PDF/exploration_price_variable.pdf)   |
| price             | 3.16     | 17.723   | Inverted "L” | Many outliers to the left | Skewed to the right | Logarithmic          |   [Appendice D](EDA/PDF/exploration_value_price_variable.pdf)    |
| Value price       | 3.13     | 17.56    | Inverted "L” | Many outliers to the left | Skewed to the right | Logarithmic          |   [Appendice E](EDA/PDF/exploration_number_of_reviews_variable.pdf)   |

On the other hand, the exploration of the “rating” variable reveals a near-normal distribution with a slight left skewness. Values of 0 and empty strings were reclassified as NA to represent missing values accurately. Given its minimal skew and generally balanced distribution, no transformation is recommended, facilitating a straightforward analysis of the dataset's ratings 

```{r}
# Create a bar plot for rating variable without fixing missing values
ggplot(sephora, aes(x = rating)) +
    geom_bar()
```

**Histogram of rating variable without any changing**
![ rating_variable_without_any_changing](https://github.com/eguzmanleano30/Exploration_Data_Analisis/assets/172155030/7b1a498a-f522-4456-9630-34cf51fa297d)

 
```{r}
# Replace 0 with NA in the rating column of sephora dataset 
sephora$rating[sephora$rating == 0] <- NA
sephora$rating[sephora$rating == ""] <- NA


# Create a bar plot for rating variable with fixing missing values
ggplot(sephora, aes(x = rating)) +
    geom_bar()
```

**Histogram of rating variable with classification of missing values** 
![ rating_variable_with_classification_missing_value](https://github.com/eguzmanleano30/Exploration_Data_Analisis/assets/172155030/7cd555df-d905-435e-8cc5-4283a30023a9)


   **_Transformation results of numerical variables._** 

The following table illustrate the results of log transformation of the variables generated distributions that approximate normality, with minimal skewness and kurtosis observed. The transformed “love”, “price”, and “value price” variables exhibit symmetric distributions, as indicated by their histograms, density plots, QQ plots, and box plots. Similarly, the log-transformed “number of reviews” variable shows a distribution close to normal, with slight skewness to the left. For additional details on each transformed variable, refer to the respective appendices: Appendix F for log love variable, Appendix G for log price, Appendix H for log value price, and Appendix I for log number of reviews. 
     

| **Log transform. Variable** | **Skewness** | **Kurtosis** | **QQ Plot**                          | **Box Plot**                          | **Histogram**                 | **Plot and code** |
| --------------------------- | ------------ | ------------ | ------------------------------------ | ------------------------------------- | ----------------------------- | ----------------- |
| Log love                    | \-0.013      | 3.02         | Normal, tiny outliers                | Normal, outliers on both sides        | Normal                        | [Appendix F](EDA/PDF/transformation_love.pdf)         |
| Log number of review        | \-0.39       | 3.02         | Normal, tiny outliers, slight uptick | Normal, few outliers left, more right | Normal, slight skewness right | [Appendix G](EDA/PDF/transformation_number_of_reviews.pdf)         |
| Log price                   | \-0.39       | 3.28         | Normal, tiny outliers, slight uptick | Normal, few outliers left, more right | Normal, slight skewness right | [Appendix H](EDA/PDF/transformation_price.pdf)         |
| Log value price             | \-0.19       | 2.34         | Normal, tiny outliers on top         | Normal, some outliers left            | Normal, slight skewness left  | [Appendix I](EDA/PDF/transformation_value_price.pdf)       |

### Exploration of binary variables.

**_Marketing Flag content variable._**

The "Marketing Flags content" variable in the Sephora dataset denotes specific marketing flags linked to products on the website, like "online only," "exclusive," or "limited edition". These flags signify promotional aspects, aiding in understanding consumer preferences and Sephora's marketing strategies. Observations marked with "0" have been replaced with NA to denote missing data as illustrate the below figures 

```{r}
# Create a bar plot for MarketingFlags_content variable without filter missing values
ggplot(sephora, aes(y = MarketingFlags_content)) +
  geom_bar() +
  coord_flip() + 
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```
  
**Histogram of Marketing Flags Content variable without any changing**
![Histogram of Marketing Flags Content variable without any changing ](https://github.com/eguzmanleano30/Exploration_Data_Analisis/assets/172155030/74d47e47-b884-4f94-8db4-b8cb5aad92a0)

```{r}
# Replace 0 with NA in the rating column of sephora dataset 
sephora$MarketingFlags_content[sephora$MarketingFlags_content == 0 |
sephora$MarketingFlags_content == 38 | sephora$MarketingFlags_content == 52 |
sephora$MarketingFlags_content == 404 | sephora$MarketingFlags_content == 28 |
sephora$MarketingFlags_content == 84] <- NA


# Create a bar plot for rating variable with fixing missing values
ggplot(sephora, aes(y = MarketingFlags_content)) +
  geom_bar() +
  coord_flip() + 
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```
**Histogram of Marketing Flags Content variable with classification of missing values**
![image](https://github.com/eguzmanleano30/Exploration_Data_Analisis/assets/172155030/de02fb57-d4aa-43e3-84e0-3dd744b3256b)


**Exploration of remanent binary variables** 

For the remaining binary variables in the Sephora dataset -- "online only," "exclusive," "limited edition," and "marketing flag" -- no observations were altered or replaced during the data preprocessing stage. These variables retain their original values as provided in the dataset. These binary indicators serve essential roles in delineating various aspects of product availability, exclusivity, and promotional status on Sephora's platform, contributing valuable insights into the marketing strategies and consumer engagement patterns within the cosmetics industry. 




