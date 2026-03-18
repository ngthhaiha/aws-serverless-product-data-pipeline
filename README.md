# Serverless Product Data Pipeline for E-commerce Analytics on AWS
A serverless AWS data engineering project that collects product data from an external product source, validates and transforms the data, stores curated records for analytics, and exposes the dataset through a REST API. The system is designed as a lightweight end-to-end pipeline for learning, internship reporting, workshop presentation, and portfolio building.

## Table of Contents
- [Project Overview](#project-overview)
- [Objectives](#objectives)
- [Business Problem](#business-problem)
- [Solution Overview](#solution-overview)
- [Architecture](#architecture)
- [Processing Flow](#processing-flow)
- [AWS Services Used](#aws-services-used)
- [S3 Storage Structure](#s3-storage-structure)
- [Product Schema](#product-schema)
- [Suggested Repository Structure](#suggested-repository-structure)
- [Prerequisites](#prerequisites)
- [Environment Setup](#environment-setup)
- [Deployment Steps](#deployment-steps)
- [End-to-End Pipeline Flow](#end-to-end-pipeline-flow)
- [Core Analytics Queries](#core-analytics-queries)
- [Expected API Endpoints](#expected-api-endpoints)
- [Expected Dashboard Components](#expected-dashboard-components)
- [Monitoring and Logging](#monitoring-and-logging)
- [Security](#security)
- [Cost Optimization](#cost-optimization)
- [Expected Outcomes](#expected-outcomes)
- [Future Improvements](#future-improvements)
- [Learning Value](#learning-value)
- [Author](#author)
- [Notes](#notes)
- [License](#license)

---

## Project Overview
In modern business environments, product data is an important asset for catalog analysis, pricing observation, category performance tracking, and internal analytics. However, many small systems and learning projects do not have a proper pipeline for automatically collecting, cleaning, storing, and serving product data.

This project builds a serverless product data pipeline on AWS to simulate a real-world workflow focused on ETL, orchestration, data warehousing, and API deployment.

The system collects product records from an external source such as a public product API, validates and transforms the data with AWS Lambda, stores staging data in Amazon S3, loads curated data into Amazon Redshift, and provides REST access through a Flask API deployed on AWS Lightsail Containers.

The project demonstrates:

- cloud-native architecture
- serverless ETL design
- workflow orchestration with Step Functions
- scheduled execution with EventBridge Scheduler
- analytical storage with Redshift
- API deployment with Flask, Docker, and Lightsail
- logging and monitoring with CloudWatch

---

## Objectives
The main objectives of this project are:

- Collect product data from an external source such as a public product API
- Validate and clean incoming product records
- Store raw or staging data in Amazon S3
- Load curated product data into Amazon Redshift
- Orchestrate ETL steps using AWS Step Functions
- Schedule ingestion jobs automatically with Amazon EventBridge Scheduler
- Expose product data through a Flask REST API
- Apply basic logging, monitoring, security, and cost control practices

---

## Business Problem
This project addresses a common analytics and data integration problem in business systems:

- How can product data be collected automatically instead of manually?
- How can inconsistent external product records be cleaned and standardized?
- How can product data be stored in a query-friendly warehouse for analysis?
- How can downstream users or applications access the processed data through an API?
- How can all of this be implemented on AWS with low operational overhead?

Without a centralized data pipeline, product data is often scattered, manually maintained, difficult to validate, and hard to reuse across dashboards, reports, or applications. This project solves that by building a structured, automated, cloud-based ETL pipeline.

---

## Solution Overview
The proposed solution uses AWS managed and serverless services to build a practical product analytics platform.

Product records are collected from an external source by AWS Lambda. The data is validated, standardized, and stored in Amazon S3 as a staging layer. AWS Step Functions orchestrates the ETL workflow, while Amazon EventBridge Scheduler triggers the workflow automatically on a schedule. Cleaned and curated records are then loaded into Amazon Redshift for analytics and structured querying. A Flask REST API is containerized with Docker and deployed to AWS Lightsail Containers so users and applications can retrieve product data through simple endpoints. Amazon CloudWatch tracks logs, metrics, and operational visibility.

This design is suitable for an AWS internship, workshop, or student project because it demonstrates:

- real business-oriented ETL use case
- serverless and managed AWS services
- end-to-end data pipeline design
- warehouse loading and API serving
- monitoring and operational thinking
- infrastructure as code with Terraform

---

## Architecture
### Proposed Architecture
```text
External Product Source
        ↓
AWS Lambda (Extract / Validate / Transform)
        ↓
Amazon S3 (Raw / Staging)
        ↓
AWS Step Functions
        ↓
Amazon Redshift
        ↓
Flask REST API
        ↓
AWS Lightsail Containers
        ↓
Amazon CloudWatch
```

### Scheduled Execution
```text
Amazon EventBridge Scheduler
        ↓
AWS Step Functions
        ↓
ETL Workflow Execution
```

## Processing Flow
The pipeline processes product data through the following steps:
1. **Amazon EventBridge Scheduler** triggers the ETL workflow on a schedule.
2. **AWS Step Functions** starts the state machine.
3. **AWS Lambda** sends requests to the external product source and retrieves product records.
4. Lambda validates required fields, standardizes formats, and prepares the dataset.
5. Raw or staging files are stored in **Amazon S3**.
6. Transformed records are loaded into **Amazon Redshift**.
7. **The Flask REST API** queries Redshift and returns product data to clients.
8. **Amazon CloudWatch** collects logs, execution history, and operational metrics.


## AWS Services Used

| Service                      | Role in the Project                                        |
| ---------------------------- | ---------------------------------------------------------- |
| AWS Lambda                   | Extracts, validates, transforms, and prepares product data |
| Amazon S3                    | Stores raw or staging datasets before warehouse loading    |
| AWS Step Functions           | Orchestrates ETL workflow steps                            |
| Amazon Redshift              | Stores curated product data for analytics and API queries  |
| Amazon EventBridge Scheduler | Triggers the ETL workflow automatically on a schedule      |
| AWS Lightsail Containers     | Hosts the containerized Flask REST API                     |
| Amazon CloudWatch            | Provides logs, metrics, and monitoring                     |
| AWS IAM                      | Manages access control using least privilege               |
| Terraform                    | Provisions AWS infrastructure as code                      |
| Docker                       | Containerizes the Flask REST API                           |
| Pytest                       | Tests source responses and pipeline assumptions            |
| AWS CLI                      | Manages AWS resources from the command line                |


## S3 Storage Structure

Data in Amazon S3 is organized using a simple staging layout:

```text
s3://<your-bucket-name>/
├── raw/
│   └── year=2026/month=03/day=18/
│       └── products_raw_001.json
├── staging/
│   └── year=2026/month=03/day=18/
│       └── products_clean_001.json
├── analytics/
│   └── query-results/
└── logs/
```

### Zone Description

- **raw/**: stores raw incoming product records from the external source  
- **staging/**: stores validated and standardized records ready for warehouse loading  
- **analytics/**: stores optional SQL query outputs or exports  
- **logs/**: stores optional operational artifacts if needed

## Product Schema

A sample product record looks like this:

```json
{
  "id": 101,
  "title": "Wireless Headphones",
  "description": "Bluetooth over-ear headphones with noise isolation",
  "category": "electronics",
  "brand": "SoundPro",
  "price": 89.99,
  "discount_percentage": 10.5,
  "rating": 4.6,
  "stock": 120,
  "source_name": "dummyjson",
  "ingestion_timestamp": "2026-03-18T09:00:00Z"
}
```

### Main Fields

| Field | Description |
|---|---|
| `id` | Unique product identifier |
| `title` | Product title |
| `description` | Product description |
| `category` | Product category |
| `brand` | Brand name |
| `price` | Product price |
| `discount_percentage` | Discount percentage if available |
| `rating` | Product rating |
| `stock` | Available stock |
| `source_name` | Data source name |
| `ingestion_timestamp` | Timestamp when the record was ingested |

## Repository Structure

```text
aws-serverless-product-data-pipeline/
├── README.md
├── docs/
│   ├── architecture/
│   ├── diagrams/
│   └── report/
├── data/
│   ├── sample/
│   └── mock/
├── etl/
│   ├── extract/
│   ├── transform/
│   ├── load/
│   └── sql/
├── lambda/
│   ├── extract_product/
│   ├── transform_product/
│   └── load_redshift/
├── api/
│   ├── app/
│   ├── Dockerfile
│   └── requirements.txt
├── infra/
│   ├── terraform/
│   ├── scripts/
│   └── iam/
├── tests/
│   ├── source_tests/
│   └── api_tests/
└── requirements/
    ├── local.txt
    └── dev.txt
```

### Folder Description

- **docs/**: architecture, diagrams, proposal, and report materials  
- **data/**: sample and mock datasets for testing  
- **etl/**: ETL scripts, transformation logic, and SQL files  
- **lambda/**: AWS Lambda source code for ETL functions  
- **api/**: Flask REST API application and Docker setup  
- **infra/**: Terraform code, IAM policies, and helper scripts  
- **tests/**: source tests, ETL tests, and API tests  
- **requirements/**: Python dependency files

## Prerequisites

Before running this project, make sure you have:

- An AWS account  
- Access to S3, Lambda, Step Functions, Redshift, EventBridge Scheduler, Lightsail, and CloudWatch  
- AWS CLI installed and configured  
- Terraform installed  
- Docker installed  
- Python 3.x  
- Git  
- A suitable AWS region  
- An S3 bucket for staging data  
- A Redshift cluster or Redshift Serverless workgroup  
- AWS Lightsail Container Service access for API deployment

## Environment Setup

## Deployment Steps

## End-to-End Pipeline Flow

The full pipeline works as follows:

1. **Amazon EventBridge Scheduler** triggers the ETL workflow  
2. **AWS Step Functions** starts the state machine  
3. **AWS Lambda** extracts product data from the external source  
4. **AWS Lambda** validates and transforms the records  
5. Raw or staging data is written to **Amazon S3**  
6. Curated records are loaded into **Amazon Redshift**  
7. The **Flask API** queries Redshift and serves product data  
8. **Amazon CloudWatch** tracks logs, errors, durations, and execution status


## Core Analytics Queries

## Expected API Endpoints

## Expected Dashboard Components

If a dashboard layer is added later, it may include:

- Total products by category
- Top brands by product count
- Average price by category
- Top-rated products
- Stock distribution
- Ingestion volume by day
- Category share by percentage

## Monitoring and Logging

### Key Metrics

- Number of product records received
- Number of successfully processed records
- Number of failed records
- Lambda duration and errors
- Step Functions execution success and failure count
- Number of API requests
- API error rate
- Data load completion status

### Monitoring Tools

- **CloudWatch Logs** for Lambda execution logs
- **CloudWatch Metrics** for request counts, durations, and errors
- **Step Functions execution history** for workflow tracing
- **Lightsail container logs** for API diagnostics
- **CloudWatch Alarms** for ETL failures or abnormal error rates

## Security

This project applies several basic AWS security practices:

- IAM least privilege
- Restricted S3 bucket access
- No hard-coded AWS credentials in source code
- Secure handling of environment variables
- Separate permissions for Lambda, Redshift access, and deployment operations
- Controlled public exposure of the API
- Optional encryption for S3 objects and warehouse storage
- Network and credential separation for development and production environments

## Cost Optimization

To keep costs low during learning and demonstration:

- Use small to medium-sized datasets
- Run the ETL job on a reasonable schedule
- Clean up unused AWS resources after testing
- Use S3 only for necessary staging data
- Keep Redshift resources at the smallest practical size
- Avoid excessive logging retention
- Monitor usage with AWS Budgets
- Stop or destroy infrastructure when the demo ends

## Expected Outcomes

By the end of this project, the expected outcomes are:

- A working serverless ETL pipeline on AWS for product data
- Automated product data ingestion and transformation
- Product records staged in S3 and curated in Redshift
- REST API access to warehouse data
- Logging and monitoring visibility for pipeline operations
- A portfolio-ready project for AWS Data Engineer Intern or Fresher roles
- Practical experience with cloud orchestration, storage, warehouse loading, and deployment

## Future Improvements

## Learning Value

By completing this project, the developer can strengthen:

- End-to-end data pipeline design skills
- Hands-on experience with AWS serverless services
- Workflow orchestration with Step Functions
- S3 staging and warehouse integration
- Data warehouse loading with Redshift
- REST API deployment with Flask, Docker, and Lightsail
- Monitoring, logging, security, and cost awareness on AWS
- Practical skills for internship, workshop, and portfolio presentation

## Author

- **Author**: Nguyen Thi Hai Ha
- **Project Type**: Internship / Personal Project / Workshop
- **Role**: AWS Data Engineer Intern

## Notes

- This is a learning and demo project built at small scale.
- Some components may be simplified to fit internship scope, time, and cost constraints.
- The external product source can be replaced with another public API or business dataset without changing the overall architecture significantly.
- If Lightsail deployment is not available, the API can be tested locally while keeping the ETL pipeline on AWS.

## License

All rights reserved for internship reporting, workshop presentation, and personal portfolio use only.
