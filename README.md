# Crowdflower Search Results Relevance

[CrowdFlower](https://appen.com/about-us/) is the leader in enterprise crowdsourcing. CrowdFlower's technology platform offers quality-ensured crowdsourcing at massive scale. The company solves problems ranging from product categorization to business lead verification to content creation. Clients from startups to the Fortune 500 enjoy increased flexibility, faster turnaround time and cost savings. The platform is acquired by Appen in Mar 2019.


### Objective:

- Create an open-source model that can be used to measure the relevance of search results. Model performance is evaluated on quadratic weighted kappa, which measures the agreement between two ratings - scores assigned by the human rater and the predicted scores.

### Clients and Impacts:

- **Small Businees Owners** : Allow to have relevant information to compete with more resource rich comeptitions on user experience on search. Save them cost on gathering the needed to data to train and test different model algorithms


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

1. Define probleem type, understand data structure
   - This is a multilabel classification problems in which features are in form of text string.
   - Identify missing values under product_description. Assign "none" for those
2. Data Cleansing/Wrangling
   - Perprocessing both product title and description with the following steps
        - Remove stopworsds, puntuncation, numeric values
        - Lemmatization to get stem root
        - Combine both product title and description into one column, convert back to text string
        - Apply TfidVectorizer to find keywords, based on median relevance score
3. Exploratory/Feature Creation
    - Compare query entry with processed text string and obtain text string simiarlity. Two methods are used
        - Spacy similarity score
        - Fuzzywuzzy partial ratio
    - Binary variables based on keywords identified by TfidVectorizer
            