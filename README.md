# Aws-airflow-project
This project demonstrates a data pipeline setup using AWS services including EC2, Lambda, S3, Redshift, and QuickSight. The pipeline extracts data from the Zillow API, processes it, and loads it into a Redshift database. Additionally, it integrates with Amazon QuickSight for basic data visualization.


![Untitled](https://github.com/user-attachments/assets/a0badb05-ab39-4290-8d43-c17a555e4e2a)

## Project Overview

1. *Extract Data*: Use Airflow to extract data from the Zillow API.
2. *Process Data*: Store the data in S3, then use Lambda functions for file movement and transformation.
3. *Load Data*: Move the processed data into Amazon Redshift.
4. *Visualize Data*: Connect Amazon QuickSight to Redshift for basic visualizations.

## Step-by-Step Setup

### 1. Set Up EC2 Instance

1. *Create an EC2 Instance:*
   - Launch an EC2 instance from the AWS Management Console.
   - Attach a security group to the instance.
   - Edit the inbound rules of the security group to allow access from your machine’s IP address.

2. *Connect to EC2 Instance:*
   - SSH into your EC2 instance using the private key associated with it.

3. *Install Python and Required Packages:*
   - Update the package list:
     bash
     sudo apt update
     
   - Install Python 3.12 and venv:
     bash
     sudo apt install python3.12 python3.12-venv
     
   - Install pip if not already installed:
     bash
     sudo apt install python3-pip
     

4. *Install and Start Airflow:*
   - Set up a Python virtual environment:
     bash
     python3.12 -m venv airflow_env
     
   - Activate the virtual environment:
     bash
     source airflow_env/bin/activate
     
   - Install Airflow:
     bash
     pip install apache-airflow
     
   - Initialize Airflow database:
     bash
     airflow db init
     
   - Start Airflow web server and scheduler:
     bash
     airflow webserver --port 8080
     airflow scheduler
     

### 2. Configure the Data Pipeline

1. *Obtain Zillow API Key:*
   - Sign up for an API key from [RapidAPI](https://rapidapi.com/) for accessing the Zillow API.

2. *Create Airflow DAG:*
   - Write an Airflow DAG (Directed Acyclic Graph) with the following tasks:
     - *Task 1: Extract Data*
       - Create a task to make a GET request to the Zillow API using Python. This task retrieves data and stores it as a JSON file.
     - *Task 2: Load Data into S3*
       - Create an S3 bucket to store the extracted JSON data.
       - Attach an IAM role to the EC2 instance with AmazonS3FullAccess permissions to allow EC2 to interact with S3.
       - Use a BashOperator to move the JSON file to the S3 bucket.

### 3. Set Up AWS Lambda Functions

1. *Create Lambda Function for File Movement:*
   - Create a Lambda function that triggers when a file is uploaded to the S3 bucket.
   - This Lambda function moves the file to a private S3 bucket.

2. *Create Lambda Function for Data Transformation:*
   - Create a second Lambda function that triggers when a file arrives in the private bucket.
   - This function transforms the JSON file into CSV format, selects specific columns, and uploads the transformed file to another S3 bucket.

3. *Create IAM Roles for Lambda Functions:*
   - Create and attach IAM roles to the Lambda functions with the necessary permissions:
     - *Role for File Movement:* Access to S3 and CloudWatch for logging.
     - *Role for Data Transformation:* Access to S3 and CloudWatch for logging.

### 4. Complete Airflow DAG Setup

1. *Sensor Task:*
   - Write an Airflow task that uses a Sensor operator to monitor the final S3 bucket for the arrival of the transformed file.

2. *Load Data into Redshift:*
   - Create an Airflow task to move the final file from the S3 bucket into the Redshift database.
   - Ensure the Redshift connection is configured properly in Airflow.

### 5. Set Up Amazon QuickSight

1. *Connect QuickSight to Redshift:*
   - Go to the [Amazon QuickSight console](https://aws.amazon.com/quicksight/).
   - Create a new data source and select Amazon Redshift.
   - Provide connection details (Redshift cluster, database name, and credentials).

2. *Create a Dataset:*
   - Import data from your Redshift database into QuickSight to create a dataset.

3. *Build Basic Visualizations:*
   - Create basic visualizations and dashboards in QuickSight based on the dataset.
   - Note: The primary focus is on building the pipeline, so visualizations are kept minimal.

## Summary

1. *EC2 Setup:*
   - Launch and configure EC2 instance.
   - Install Python, Airflow, and dependencies.

2. *Airflow DAG:*
   - Create tasks for data extraction, loading to S3, and handling file movement.

3. *Lambda Functions:*
   - Create and configure Lambda functions for file movement and data transformation.
   - Set up IAM roles for Lambda permissions.

4. *Final Airflow Tasks:*
   - Implement sensor tasks and data loading into Redshift.

5. *QuickSight Integration:*
   - Connect QuickSight to Redshift.
   - Create basic visualizations to explore the data.

By following these steps, you will establish a comprehensive data pipeline integrating data extraction, processing, storage, and visualization using AWS services
