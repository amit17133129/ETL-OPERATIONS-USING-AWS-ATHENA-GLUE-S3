# ETL-OPERATIONS-USING-AWS-ATHENA-GLUE-S3

<p align="center">
  <img width="1000" height="600" src="https://miro.medium.com/max/3840/1*-cs8hCI7V2ONXQtSPau6IQ.gif">
</p>

## What is ETL ?
ETL (Extract, Transform, Load) is the process of extracting data from disparate sources, transforming it into a clean and analysis-ready format, and loading it into a data warehouse for analysis.

<p align="center">
  <img width="1000" height="600" src="https://miro.medium.com/max/700/1*PmQcfVPZZOkafuM_vwR50w.gif">
</p>  

## EXTRACT
During data extraction, raw data is copied or exported from source locations to a staging area. Data management teams can extract data from a variety of data sources, which can be structured or unstructured. Those sources include but are not limited to:
- SQL or NoSQL servers
- CRM and ERP systems
- Flat files
- Email
- Web pages

## Transform
In the staging area, the raw data undergoes data processing. Here, the data is transformed and consolidated for its intended analytical use case. This phase can involve the following tasks:
- Filtering, cleansing, de-duplicating, validating, and authenticating the data.
- Performing calculations, translations, or summarizations based on the raw data. This can include changing row and column headers for consistency, converting currencies or other   units of measurement, editing text strings, and more.
- Conducting audits to ensure data quality and compliance
- Removing, encrypting, or protecting data governed by industry or governmental regulators
- Formatting the data into tables or joined tables to match the schema of the target data warehouse.

## Load
In this last step, the transformed data is moved from the staging area into a target data warehouse. Typically, this involves an initial loading of all data, followed by periodic loading of incremental data changes and, less often, full refreshes to erase and replace data in the warehouse. For most organizations that use ETL, the process is automated, well-defined, continuous and batch-driven. Typically, ETL takes place during off-hours when traffic on the source systems and the data warehouse is at its lowest.

## Why use ETL?
Data warehousing and ETL make up two layers of the data analytics stack. Enterprises use data warehouses to store and access cleansed, consistent, and high-quality business data, sourced from diverse origin systems. ETL is the set of methods and tools used to populate and update these warehouses.
Once an ETL process has replicated data to the data warehouse, analytics and business intelligence teams — data analysts, data engineers, data scientists — and SQL-savvy business users can all work with it.

# ETL OPERATIONS ON AWS

## What is Athena ?
Amazon Athena is an interactive query service that makes it easy to analyze data in Amazon S3 using standard SQL. Athena is serverless, so there is no infrastructure to manage, and you pay only for the queries that you run.

Athena is easy to use. Simply point to your data in Amazon S3, define the schema, and start querying using standard SQL. Most results are delivered within seconds. With Athena, there’s no need for complex ETL jobs to prepare your data for analysis. This makes it easy for anyone with SQL skills to quickly analyze large-scale datasets.

Athena is out-of-the-box integrated with AWS Glue Data Catalog, allowing you to create a unified metadata repository across various services, crawl data sources to discover schemas and populate your Catalog with new and modified table and partition definitions, and maintain schema versioning.

## What Is Glue?
AWS Glue is a fully managed ETL (extract, transform, and load) service that makes it simple and cost-effective to categorize your data, clean it, enrich it, and move it reliably between various data stores and data streams. AWS Glue consists of a central metadata repository known as the AWS Glue Data Catalog, an ETL engine that automatically generates Python or Scala code, and a flexible scheduler that handles dependency resolution, job monitoring, and retries. AWS Glue is serverless, so there’s no infrastructure to set up or manage.

AWS Glue is designed to work with semi-structured data. It introduces a component called a dynamic frame, which you can use in your ETL scripts. A dynamic frame is similar to an Apache Spark dataframe, which is a data abstraction used to organize data into rows and columns, except that each record is self-describing so no schema is required initially. With dynamic frames, you get schema flexibility and a set of advanced transformations specifically designed for dynamic frames. You can convert between dynamic frames and Spark dataframes, so that you can take advantage of both AWS Glue and Spark transformations to do the kinds of analysis that you want.

You can use the AWS Glue console to discover data, transform it, and make it available for search and querying. The console calls the underlying services to orchestrate the work required to transform your data. You can also use the AWS Glue API operations to interface with AWS Glue services. Edit, debug, and test your Python or Scala Apache Spark ETL code using a familiar development environment.

## What is S3?
Amazon Simple Storage Service (Amazon S3) is storage for the Internet. It is designed to make web-scale computing easier.

Amazon S3 has a simple web services interface that you can use to store and retrieve any amount of data, at any time, from anywhere on the web. It gives any developer access to the same highly scalable, reliable, fast, and inexpensive data storage infrastructure that Amazon uses to run its own global network of web sites. The service aims to maximize benefits of scale and to pass those benefits on to developers.

This introduction to Amazon Simple Storage Service (Amazon S3) provides a detailed summary of this web service. After reading this section, you should have a good idea of what it offers and how it can fit in with your business.

This guide describes how you send requests to create buckets, store and retrieve your objects, and manage permissions on your resources. The guide also describes access control and the authentication process. Access control defines who can access objects and buckets within Amazon S3, and the type of access (for example, READ and WRITE). The authentication process verifies the identity of a user who is trying to access Amazon Web Services (AWS).

## ETL WORKFLOW

Let’s start with our main agenda for writing this article. 
- We will create a bucket(folder) in s3 and upload data in .csv format. Here, i will upload only small size of data just for creating the workflow of ETL. 
- After loading the data into s3 we will navigate towards glue. 
- We will add crawler in glue. Adding crawler to glue will help us to take the data from s3 and create metadata in database table in glue. These metadata will helps us to write SQL queries in Athena. So we can navigate towards athena to write SQL queries to take some insights. 
- While writing the SQL queries we have to save the outputs of sql queries to some storage here we will be using s3. We will create a separate subfolder for storing the outputs. 
- After that we will come back to glue and add a job. Glue Jobs are meant to use spark using python or scala. Here we will be using pyspark. 
- While creating a glue job we will require the destination to store the outputs of pyspark jobs. Here also we have to use the same s3 bucket but in different subfolders. 
- We will not run these jobs, we will use glue triggers. These triggers we will dun on our respective scheduled time. 
- At the end we will create a workflows of the whole setup. Glue provides us a separate section to create a workflows.

## Creating IAM role
Search for IAM and navigate to `IAM` → `Roles` → `create role` → `specify role name` → `select role policies (Administraion acess, AWSGlueServiceRole)` → `create role`.

<p align="center">
  <img width="1000" height="500" src="https://miro.medium.com/max/1167/1*om0Uwf1d9xrS0P4gOcWaww.png">
</p>

## Creating Bucket & Uploading data to S3:
Create a bucket by giving unique name. Here, i have given `godigitaldiabetes`.

<p align="center">
  <img width="1000" height="200" src="https://miro.medium.com/max/1167/1*_YipJKvQeXw0O8DeZ2t6nQ.png">
</p>

You can get the diebetes.csv file here in uci machine learning repository diabetes.csv. Then you can upload the respective data manually by clicking on upload button. As you can see below i have uploaded diabetes.csv file to s3 bucket.

<p align="center">
  <img width="1000" height="200" src="https://miro.medium.com/max/1167/1*PL65kWx_CB-O1XoRP6gy9A.png">
</p>

## Adding Crawler
After that navigate to aws glue. On the left panel you will see crawlers. Click on crawler and click on `add crawler`.

<p align="center">
  <img width="1000" height="200" src="https://miro.medium.com/max/1167/1*w9q421cfxRxGtbFPS2yimA.png">
</p>

Then you have to write the name of the crawler. Rest all things are optional you can leave as off now and click on `next` to `add storage`.

<p align="center">
  <img width="1000" height="250" src="https://miro.medium.com/max/1167/1*hL_0KCzj-1OK45KE8OlknQ.png">
</p>

Next you have to specify crawler source type. Select Data Sources in `Crawler source type` and `Crawl all folders in Repeat crawls of S3 data stores` then `click next`.

<p align="center">
  <img width="1000" height="400" src="https://miro.medium.com/max/1167/1*1uO6Ivp2fmzEP3sokHhpwQ.png">
</p>
