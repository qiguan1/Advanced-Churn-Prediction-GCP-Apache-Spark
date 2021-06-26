# Advanced-Churn-Prediction-Using-GCP-and-Apache-Spark

<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#overview-of-the-problem">Overview of the Problem</a>
    </li>
    <li>
      <a href="#data">Data</a>
    </li>
    <li>
      <a href="#cloud-infrastructure">Cloud Infrastructure</a>
    </li>
    <li>
      <a href="#model-development">Model Development</a>
    </li>
  </ol>
</details>



<!-- OVERVIEW OF THE PROBLEM -->
## Overview of the Problem

We use KKBOX as a use case to discover the customer churn rate for a music
subscription service provider. Through our study, we utilize our findings and
approaches as a service for other music streaming companies to accurately predict
customer churn and take early interventions to improve the overall retention.

For a subscription business, accurately predicting churn is critical to long-term success. Even slight  variations in churn can drastically affect profits. In this project, we will focus on predicting the  likelihood of whether a customer will churn based on their demographic information, customer  behavior data, and transaction data. If we could predict the churn precisely, the customer service team could apply different strategies to reduce the churn rate. Our ultimate goal is to retain our customers! 

<!-- Data -->
## Data

We used the dataset provided by KKBOX on [Kaggle](https://www.kaggle.com/c/kkbox-churn-prediction-challenge/data). These were the datasets provided: 

1. train_v2.csv (training dataset with user_id and churn indicator = 0/1). 
2. sample_submission_zero.csv (testing dataset with user_id only and a dummy is_churn column)
3. transactions_v2.csv (users’ transaction data that contains user id, payment method, length of  membership plan in days, list price, paid-price, auto-renew indicator, transaction date, membership expiration date, and whether or not the user canceled the membership in this  transaction). 
4. user_logs.csv (daily user logs describing listening behaviors of an user: user id, log date, number  of songs played less than 25% of the song length, between 25% to 50% of the song length,  between 50% to 75% of of the song length, between 75% to 98.5% of the song length, and  over 98.5% of the song length, number of unique songs played, and total seconds played). 
5. members.csv (users demographic information that contains user id, city, age, registration  method, registration time, and subscription expiration date). 

<!-- Cloud Infrastructure -->
## Cloud Infrastructure

Most companies store their customer data on the cloud nowadays, as cloud technologies allow much  more flexibility and ability to scale than local storage. Therefore, we conducted our analysis and built our model entirely on the Google Cloud Platform so that most companies can easily adopt it. 
First, we created a Dataproc cluster on Google Cloud. Dataproc is a managed Spark and Hadoop service provided by Google Cloud that enables open source data tools for batch processing, querying, and machine learning.

Dataproc Overview:
![Dataproc Overview](https://user-images.githubusercontent.com/35647593/123499073-e5d4f580-d5e8-11eb-9196-7526456036b2.png)

Dataproc Cluster Setup via G-Cloud CMD:
![Dataproc cluster setup via gcloud command](https://user-images.githubusercontent.com/35647593/123499081-02712d80-d5e9-11eb-8f7c-91eef02ca821.png)

Our Dataproc cluster runs on virtual machines at low cost and fast speed. Companies can adopt this method to build models with high speed and scalability at a low price and free themselves from hardware constraints. Since our data source consists of multiple tables and nearly one million rows, we uploaded the given CSV files to Google Cloud Storage and migrated them to BigQuery. BigQuery allows superb query capabilities and low latency, and it works seamlessly with our Dataproc cluster. 

BigQuery Overview:
![BigQuery](https://user-images.githubusercontent.com/35647593/123499158-77dcfe00-d5e9-11eb-80f6-17f33b8f9a62.png)

We then used JupyterLab to develop our code and set up BigQuery connectors with PySpark to load and query our datasets. Our code is completely developed in PySpark.
![JupyterLab](https://user-images.githubusercontent.com/35647593/123499167-8e835500-d5e9-11eb-95a3-954e77e9ac1f.png)

<!-- Model Development -->
## Model Development

### Balance Dataset
Before deploying the final machine learning model, we need to process the imbalanced data. Currently, in the training dataset, we have 679,119 rows of not_churn but only 46,603 of churn. Having highly imbalanced data can significantly impair model validity as the algorithm can ignore the minority in favor of the majority class. Therefore, we chose to use the under-sample method to balance the dataset. We downsampled the majority class without replacement, which is not_churn in this case, to achieve a 60/40 balance — (Not churn: 68086; Churn: 46603).

### Model Building and Performance
With a balanced dataset, we are able to build the model to predict the churn rate. We decided to randomly split the dataset into 80% for training and 20% of data for validation. We used Random Forest because it reduces overfitting in decision trees and generally outperforms traditional classification algorithms such as logistic regression. After hyperparameter tunning with cross-validation, our best model achieved a high accuracy of **91%** and an AUC of **0.907** on the out-of-sample validation data.

We also listed the top 20 features that contributed to customer churn the most. From the plot below, we discovered that the number of transactions, average payment plan days, average plan list price, auto-renew, payment method 41, 32, 38, and registration method 7 are highly relevant to churn prediction.
![Feature Importance](https://user-images.githubusercontent.com/35647593/123499204-e326d000-d5e9-11eb-8446-e058e076f121.png)
   



