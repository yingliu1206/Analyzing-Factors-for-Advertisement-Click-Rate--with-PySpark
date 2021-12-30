# Big Data Project

## Executive Summary

   * **Background:** An online advertising company has two main types of ads on its website, contextual ads and non-contextual ads. 
   
   * **Goal:** The goal of the project is to study the factors that affect the click event so that the company can improve the service quality from these aspects so as to increase the click rate, and bring more benefits for product companies, which will finally attract more customers to post these two kinds of advertisements on this website.
   
   * **Method:** Firstly, through the EDA analysis of ads information and users information, the quantity and average price of ads in different regions and types was displayed. What's more, the mostly used browser family and device was also showed. Then merged the relevant datasets, deleted the missing values, and prepared the merged dataset for training the model. As it is a binary classification problem, the logistic regression model was adopted. The label class is "IsClick_class" and the variables include 'AdID', 'Position', 'HistCTR', 'CategoryID', 'Price' and 'Title'.
   
   * **Result:** The product companies of category '34' are the largest customers of the online advertising company. Besides, most of non-contextual ads targeted at location '3953' and '3960'. For users, the browser family '25' and the device '2014' were the most popular. Last but not the least, the logistic regression model with position of the ad, history-based estimation of click-through rate, type of ads, price for an ad, and ads title can be used to predict the click event.
   
   * **Future work:** The future work can focus on finding the missing column which recorded user's phone request in the data 'combined_stream' so that we can take it as the label class to research the important factors that may affect the click event on non-contextual ads.

## Datasets

The whole dataset consists of 7 files. According to their features, I divided them into two categories, one for contextual ads and one for non-contextual ads:
   * Contextual ads category includes 'search_click'.
   * Non-contextual ads category includes 'combined_stream'.
   * 'ad_info', 'search_info', 'location', 'category', and 'user_info' act as base datasets for the two categories. 
   
Therefore, the analysis was also segmented into two parts. For contextual ads, data 'search_click' and 'ad_info' can be joined such that we can get a merged dataset with 7 unique fields to build models. Similarly, data 'combined_stream', 'ad_info', and 'search_info' can be joined to form a merged dataset for non-contextual ads. The goal is to make a click prediction model for both contextual and non-contextual ads.

## Methods

### Tools

   * SparkSession was created at first to manage the big data.
   * Spark.read was used to read in all the files.
   * Spark.sql was used to explore the data, like merge, count, and summarize.
   * describe() got summary statistics.
   * createOrReplaceTempView() converted dataframes into SQL tables. 
   * df.na.drop() cleaned the data by dropping NA values.
   * df.toPandas() converted the data into 'pandas.core.frame.DataFrame' format.
   * The matplotlib package was used to visualize the SQL result.
   * Transformers and encoders were utilized to perform feature engineering.
   * LogisticRegression was adopted to build the model to predict whether there is click on the contextual ads. 
   * Pipeline was used to chain the process.

### EDA part

After loading the whole dataset, ads information and users information were explored.

**For ads information**, I got:
   * summary statistics of ads' price, eg. mean value, standard deviation, minimum and maximum value
   * the average price of ads for different categories
   * the number of ads of different categories
   * the average price of non-contextual ads targeting at different locations
   * the number of non-contextual ads for different locations

**For users information**, I got:
   * the mostly used browser family 
   * the mostly used device type
   
### Data preparation

  1. Registered 'search_click' and 'ad_info' the two dataframes as the SQL tables 
  2. Joined the datasets by 'left join' syntax
  3. Categorized the column IsClick into a new column called "IsClick_class"
  4. Checked the fields types 
        * IsClick_class and title must are STRING
        * the remaining fields are INT

### Hypothesis

Position, HistCTR, Category, Price, and Title are statistically significant variables which can influence the IsClick_class. 

### Model

Logistic regression is a type of regression analysis in statistics used for prediction of outcome of a categorical dependent variable from a set of predictors. So in order to pinpoint the most important factors of click as well as predict the IsClick_class, it was adopted in this case. 

StringIndexer was used to index each categorical column and then OneHotEncoder converted the indexed categories into one-hot encoded variables. The resulting output has the binary vectors appended to the end of each row. Next, VectorAssembler combined all the feature columns into a single vector column. Pipeline chained multiple Transformers and Estimators together to specify the machine learning workflow.

After training the model, I ploted the ROC curve and calculated the AUC value to evaluate the model performance. The AUC value is 0.72 more than 0.5, which means a good performance of prediction.
   
## Challenges 

### Technical Challenges 

- Plots did not show up in the output field.
    - It happened because I did not give an action command. After adding "plt.show()", the plots successfully showed up.

- When trying to import matplotlib and make a plot, jupyter notebook always showed "The kernel appears to have died. It will restart automatically."
    - As the code runed well before, I guessed the problem was caused by the kernel or the cluster interruption. So I restarted the cluster and kernel, uninstalled matplotlib, then reinstalled it, and runed the code again. Fortunately, it worked.
    
- Though I successfully trained a logistic regression model with train.limit(100), I failed to train it on the whole training set.
    - I first ruled out the code problem because it worked out in a small training set. The problem may lie in the whole training set. So I checked if any of the variables contained the missing values and moved them. After preparing the data, I trained the model again, which succeed this time. 

### Non-technical Challenges

- The complex relationship among the datasets makes it hard to use them appropriately.
    - After carefully reading each dataset description and inspecting the Entity Relation Diagram, I tried to figure out the special function of these 7 files and draw a relationship plot on my own. Finally, I found that the data 'search_click' was only for contextual ads while 'combined_stream' was only for non-contextual ads. Both of them were from the remaining 5 files.

- Failed to find the column which can be interpreted as user's phone request event in the 'combined_stream'.
    - When trying to find the label class in the combined_stream, I found there was no column relating with user's phone request event. So I searched the dataset online but found nothing useful. I also resorted to TA. She adviced me to consider it as a dataset with missing values, which is very common in reality. So I chose to only study the contextual datasets instead.

## Conclusions

- From the EDA analysis:
   
   - The online advertising company has overall 36892885 ads and the average ads price was around 4651622.64 dollars. Specifically, the highest average price of ads lied in the category '19', whereas the lowest one lied in the category '8'. Category '34' ads always preferred to be posted on this advertising company, while it had the least Category '8' ads customers. Therefore, I inferred that the company has adopted appropriate promotion methods for category '34' ads, which has brought satisfying results for the products advertised, such as high sales volume, so that the product company is more willing to put this kind of advertisements in this company website. On the contrary, the company needs to adjust the promotion mode of category '8' ads so as to help them get more attention and finally achieve a sales increase to attract more this kind of product companies.

   - Most of non-contextual ads targeted at location '3953' and '3960'. The average price of non-contextual ads at location '3682' was the highest.

   - For users information, the mostly used browser family was the type '25' and the mostly used device was the type '2014'. Hence the company can compare whether the interface of type '25' browser is more attractive to customers, and whether the device type of category '8' is more reader-friendly, so as to find the factors that attract customers and improve the service quality.

- From the model:

   - Position, HistCTR, Category, Price, and Title are important variables which can influence the click event. Therefore, the company can pay special attention to these aspects so as to increase the click rate. The higher the click rate, the higher the attention of ads, and the more likely these ads will bring profit. 

## Future work

In the future, we can try to find the missing column which recorded user's phone request in the data 'combined_stream' so that it can be taken as the label class to be predicted. Then, similar to the method of analyzing contextual ads, we can join the relevant datasets with 'combined_stream', select the variables that may be helpful to predict the label, and utilize Transformers and Encoders to prepare the data for training the model. 

If it is a binary classification problem, the logistic regression model can be adopted. However, if it is a multiple classification problem, models such as decision tree and random forest can also be tried. After building the model, evaluate its' accuracy. For example, the larger the AUC value, the better the performance of the model, which determines whether we should use it to predict.

In the end, combined with the previous research on the click event of contextual ads, we can get the statistically significant factors that can affect the click event on the two main types of advertisements on the company's website so that the company can increase the click rate and bring more benefits for product companies, which will finally attract more customers to post advertisements on this website.

## Takeaways from the course

-   Learned how to use ancillary tools that support big data processing, including git and the Linux command line. 
-   Learned big data concepts and terms and realized the differences between data science and data engineering.
-   Learned the concept of parallelism and how to put it into practice with Python.
-   Learned the concept of cloud and the cloud services supported by Microsoft Azure and Amazon Web Services.
-   Learned Hadoop's history, design and influence on the big data world.
-   Learned how MapReduce works and how to write a MapReduce job with languages other than Java using Hadoop Streaming, and how to scale out.
-   Learned the main pillars of Spark, including Spark RDDs and DataFrames, SparkSQL, SparkML, and Spark Streaming.
-   Learned the Azure Databricks and Dask.
-   Learned some sample Applications of big data processing with Spark, such as NLP with John Snow Labs' spark-nlp, and Bioinformatics.

## Code files

project.ipynb
