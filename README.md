# AWS Data Analysis Project ☁️

-----

## Overview

This project demonstrates an end-to-end data analytics pipeline on AWS, transforming raw CSV data into actionable business insights through cloud-native services.
The solution simulates a real-world scenario where sales data is ingested, processed, queried, and visualized to support decision-making.

Since I have experience in the logistics field, I chose a dataset related to technology product sales.

-----

## Pipeline

                                                     Data Ingestion (CSV)
                                                                          
                                                             ⬇️    
                                                                       
                                                     Amazon S3 (Data Lake) 
                                                                   
                                                             ⬇️

                                                     AWS Glue (Data Catalog) 
                                                               
                                                             ⬇️

                                                     Amazon Athena (Query Engine)
                                                    
                                                             ⬇️

                                                     Amazon QuickSight (Visualization)

-----

To upload the CSV file, we first create the S3 bucket and then upload the dataset:

     ~ $ aws s3 mb s3://bucket-tech-sales-csv
     ~ $ aws s3 cp tech-sales-dataset.csv s3://bucket-tech-sales-csv/

------

Before cataloging the data in AWS Glue, we must create an IAM role that allows Glue to access the S3 bucket. The trust policy is defined in JSON format:

     ~ $ cat <<ati > glue-trust-policy.json 
      > {"Version": "2012-10-17", "Statement": [{"Effect":"Allow", "Principal": {"Service": "glue.amazonaws.com"}, "Action": "sts:AssumeRole"}]}
      > ati
      
-----

Then, create the role and attach the required policies:

     ~ $ aws iam create-role --role-name GlueS3AccessRole --assume-role-policy-document file://glue-trust-policy.json

     ~ $ aws iam attach-role-policy --role-name GlueS3AccessRole --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess

     ~ $ aws iam attach-role-policy --role-name GlueS3AccessRole --policy-arn arn:aws:iam::aws:policy/service-role/AWSGlueServiceRole
     
-----

Next, create the database and the crawler:

     ~ $ aws glue create-database --database-input Name=techsales_db
     ~ $ aws glue create-crawler --name sales-crawler --role GlueS3AccessRole --database-name techsales_db --targets S3Targets=[{Path="s3://bucket-tech-sales-csv/raw/}"]

----- 

Finally, start the crawler:

     ~ $ aws glue start-crawler --name sales-crawler

----- 

The dashboard was divided into different sections based on key business metrics, improving analysis and decision-making.

First, a KPI section provides a quick overview of performance, including Total Sales, Total Profit, and Best-Selling Product.

Then, the dashboard includes segmented visualizations such as:

Profit by region, identifying high-performing markets
Top-selling products by quantity, highlighting demand
Sales by year, showing revenue trends over time
Sales by category, enabling product comparison
Payment methods by region, providing customer behavior insights

This structure separates high-level overview from detailed analysis (drill-down), improving clarity and usability in Amazon QuickSight. It allows users to quickly understand the overall scenario and then explore specific business insights.

![dash](assets/Quick-Suight-Dashboard.pdf)
