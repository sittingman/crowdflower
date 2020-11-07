# Crowdflower Search Results Relevance

[CrowdFlower](https://appen.com/about-us/) is the leader in enterprise crowdsourcing. CrowdFlower's technology platform offers quality-ensured crowdsourcing at a massive scale. The company solves problems ranging from product categorization to business lead verification to content creation. Clients from startups to the Fortune 500 enjoy increased flexibility, faster turnaround time, and cost savings. The platform is acquired by Appen in Mar 2019.


### Objective:

- Create an open-source model that can be used to measure the relevance of search results. Model performance is evaluated on quadratic weighted kappa, which measures the agreement between two ratings - scores assigned by the human rater and the predicted scores.

### Clients and Impacts:

- **Small Business Owners** : Allow to have relevant information to compete with more resource-rich competitors on user experience on search. Save them cost on gathering the needed data to train and test different model algorithms


### Data Source

- [Kaggle](https://www.kaggle.com/c/crowdflower-search-relevance/data)
    - To obtain data, download from the link above, unzip and put all csv files into a folder called "data" OR using the script [HERE](https://github.com/sittingman/crowdflower/blob/master/0.obtain_data.ipynb)
    
- Data Dictionary
    - id: Product id
    - query: Search term used
    - product_title: The full name of the product
    - product_description: The full product description along with HTML formatting tags
    - median_relevance: Median relevance score by 3 raters. This value is an integer between 1 and 4. 
    - relevance_variance: Variance of the relevance scores given by raters. 
    
### Outline of Approach

1. Define problem type, understand the data structure
   - This is a multilabel classification problem in which features are in form of text strings.
   - Identify missing values under product_description. Assign "none" for those
2. [Data Cleansing/Wrangling](https://github.com/sittingman/crowdflower/blob/master/1.data_expo.ipynb)
   - Preprocessing both product title and description with the following steps
      - Remove stopwords, punctuation, numeric values
      - Lemmatization to get stem root
      - Combine both product title and description into one column, convert back to string
      - Apply TfidVectorizer to find keywords, based on the median relevance score
3. [Exploratory/Feature Creation](https://github.com/sittingman/crowdflower/blob/master/1.data_expo.ipynb)
   - Compare query entry with a processed text string and obtain text string similarity. Two methods are used
      - Spacy similarity score
      - Fuzzywuzzy partial ratio
   - Binary variables based on keywords identified by TfidVectorizer
   - Category variable based on query word counts
   - Findings: High median relevance appears to have high similarity and fuzzywuzzy score. Longer search query has a lower average median score
4. [Machine Learning](https://github.com/sittingman/crowdflower/blob/master/2.mach_learn.ipynb)
   - Applied dataset into Logistics Regression, Support Vector Machine, Random Forecast, and Extreme Gradient Boosting
   - Model Performance Summary (Kappa, weights='quadratic')
   
   | Model | Kaggle | Time |
   | --- | --- |--- |
   | Logistic Regression | 0.497 | 4s |
   | SVC  | 0.483 | 4m 20s |
   | ExtraTrees | 0.554 | 3m 40s |
   | Random Forecast | 0.539 | 3m 14s |
   | XGBoost | 0.532 | 9m 3s |
   
   
### Recommendations/Next Steps

Tree-based ensemble models such as ExtraTrees and Random Forecast performed the best in this analysis. From the absolute quadratic kappa score standpoint, ExtraTree performs the best and will be the winning model.

Note that the dataset target attribute is imbalanced, with 60% of rating being 4. We may form an assumption that users tend to give positive when they find the query result useful, but may not even give any rating when the search results are bad, which explained why only 8% of ratings were 1. Clients can leverage the result in two ways:
- Study the query results of 4, identify key drivers on why those results had good ratings, and apply that search algorithm into more product categories
- Study the query results of 1, plus queries that didn't have a rating, to draw key drivers on why the search results were not useful, and improve search algorithms on those

One more powerful information to obtain is whether the product recommended results in purchases. This information would help narrow down the most effective query/product combination, which will help clients to improve conversion on their site.