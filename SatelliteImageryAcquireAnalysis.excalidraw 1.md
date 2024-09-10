

### Satellite Image Acquisition and Analysis System Design

## Design is Scalable, Extensible, and Efficient System for Satellite Imagery Acquisition and Analysis

# Introduction
This document outlines the design of a robust, scalable, extensible, and efficient system that aims to acquire satellite imagery from various sources and perform analysis tasks on the acquired data. The system is designed to be scalable, extensible, and efficient, taking into consideration real-world constraints such as limited bandwidth and storage capacity.

## Table of Contents
1. [Introduction](#introduction)
2. [Step 1: Use Cases and Constraints](#step-1-use-cases-and-constraints)
   - [Use Cases](#use-cases)
   - [Constraints](#constraints)
   - [Assumptions](#assumptions)

## Step 1: Use Cases and Constraints

The system is designed to handle the following use cases and constraints:

### Use Cases

1. **Acquire Satellite Imagery**:
   - Connects to multiple satellite imagery providers.
   - Retrieves images based on parameters like area of interest, time frame, and resolution.
   - Stores raw imagery in scalable storage.

2. **Define and Execute Analysis Pipelines**:
   - Users define a sequence of processing steps (e.g., vegetation index calculation).
   - Executes the defined processing pipeline on the acquired imagery.
   - Stores and provides access to the analysis results.

3. **Retrieve Processed Results**:
   - Users can query and retrieve processed images and analysis outputs.
   - The system provides access to the results through an API, user interface, or web interface.

### Constraints

1. **Data Volume and Storage**: The system should efficiently handle large volumes of high-resolution imagery and store it.
2. **Processing Time**: The system should execute analysis pipelines within acceptable time frames to provide timely results.
3. **Integration Flexibility**: The system must seamlessly integrate with various data formats and APIs from different satellite image providers.
4. **Scalability**: The system should easily scale horizontally to handle varying loads and high processing demands.
5. **Security**: The system must prioritize data security and comply with relevant privacy regulations.
6. **Efficiency**: The system should optimize resource utilization and minimize processing time to achieve efficient performance.


## Assumptions

The system is designed with the following key assumptions:

1. **Data Acquisition**: The system will seamlessly acquire satellite imagery from multiple providers, irrespective of the data formats used by each provider.

2. **Processing Pipelines**: Users will have the flexibility to define custom processing pipelines by selecting from a set of predefined steps, ensuring tailored analysis workflows.

3. **Resource Management**: The system will dynamically scale its computational and storage resources based on real-time demand and workload, ensuring optimal performance and efficiency.

4. **Data Quality**: The acquired imagery from different providers will adhere to agreed-upon quality standards, guaranteeing accurate and reliable analysis results.

5. **User Interaction**: Users will interact with the system through a user-friendly API or interface, enabling them to easily submit requests and retrieve results.

5. **User Interaction**: Users will interact with the system through a user-friendly API or interface, enabling them to easily submit requests and retrieve results.

6. **Resource Management**: The system will dynamically scale its computational and storage resources based on real-time demand and workload, ensuring optimal performance and efficiency.

7. **Data Quality**: The acquired imagery from different providers will adhere to agreed-upon quality standards, guaranteeing accurate and reliable analysis results.


### Step 2: High level design

## Step 3: Design Core Components

### 1. Client Interface
This is the entry point for client requests. Clients initiate requests to process satellite imagery by specifying parameters such as time frame, area of interest, image provider, and type. These requests are sent to the API Gateway.

### 2. API Gateway
This component manages and secures client requests by handling authorization, authentication, and routing. It receives requests from clients and forwards them to the Analysis Ready Data Service (ARDS) or triggers a Lambda function for image acquisition. The API Gateway is designed to provide a secure and scalable interface, handling a large volume of requests with low latency.

### 3. Analysis Ready Data Service (ARDS)
This service handles requests related to metadata and the state of satellite imagery. It checks if the requested imagery is available, manages area of interest administration, and handles ad-hoc requests. ARDS queries MongoDB for metadata and request states. If imagery is available, it retrieves it for processing. If imagery is not available, ARDS triggers a Lambda function to order new imagery. ARDS centralizes the management of metadata and request states, facilitating efficient image retrieval and request handling.

### 4. AWS Lambda
This service executes code in response to triggers, such as ordering new imagery when requested imagery is not available. It is triggered by the API Gateway, orders imagery from satellite providers, updates ARDS, and monitors the status of imagery acquisition. Lambda offers a serverless, on-demand execution environment, which is efficient and cost-effective for handling image ordering and status updates.

### 5. Amazon Elastic File System (EFS)
This component provides scalable and shared storage for satellite imagery, accessible by multiple EC2 instances. EFS stores acquired imagery, making it available for processing by EC2 instances. EFS is essential for handling large volumes of imagery and provides scalable concurrent access to file storage.

### 6. Amazon SQS (Simple Queue Service)
This service facilitates message-based communication for event-driven processing. It signals when new imagery is available for processing. SQS receives events from the system, such as when new imagery is stored in EFS, and triggers image processing workflows. SQS enables decoupling of components, allowing asynchronous communication and reliable message delivery.

### 7. EC2 Instances
These instances perform image processing tasks by retrieving imagery from EFS, executing processing algorithms, and handling large-scale computation. EC2 instances pull imagery from EFS based on SQS events and process it according to the defined pipeline. EC2 provides scalable computing resources, allowing for flexible and cost-effective management of processing loads.

### 8. Apache Airflow
This tool manages and orchestrates complex workflows for image processing, including scheduling and monitoring tasks. It coordinates the execution of processing steps and ensures that tasks are executed in the correct sequence. Apache Airflow provides a powerful platform for managing workflows, which is essential for orchestrating various processing steps.

### 9. AWS Batch
This service manages and executes batch processing jobs for handling large-scale computational tasks. It executes parallel processing jobs defined by the processing pipeline and scales resources as needed based on job requirements. AWS Batch offers efficient, scalable, and cost-effective batch processing by automating compute resource management and job execution.

### 10. Amazon S3
This component stores processed imagery and analysis results. It receives and stores output from image processing tasks and provides durable and scalable storage for results. S3 offers high durability, scalability, and availability, making it ideal for storing large volumes of processed data.

### 11. DynamoDB
This service stores metadata and structured results for quick lookups and efficient querying. It stores and retrieves metadata related to processed imagery, providing fast and predictable performance with seamless scalability.

![System Architecture Diagram](https://github.com/anatmiller/SatelliteImageryAnalysis_SystemDesign_Assignment/blob/main/SatelliteImageryAcquireAnalysis.excalidraw.pdf)

Design Justification
## Design Justification

### Scalability
Scalability is achieved through the use of several key components:
- **EC2 (Elastic Compute Cloud)**: Provides scalable computing resources that can be adjusted based on the workload. EC2 instances can be added or removed dynamically to handle varying loads, ensuring efficient processing of large volumes of satellite imagery.
- **EFS (Elastic File System)**: Offers scalable file storage that can grow and shrink automatically as files are added and removed. EFS supports concurrent access from multiple EC2 instances, making it ideal for large-scale image processing tasks.
- **S3 (Simple Storage Service)**: Provides highly scalable object storage for storing raw and processed imagery. S3 can handle virtually unlimited amounts of data, ensuring that storage capacity is never a bottleneck.
- **AWS Batch**: Manages and executes batch processing jobs, automatically scaling compute resources based on the volume and complexity of the tasks. This ensures that large-scale processing jobs are handled efficiently without manual intervention.

### Extensibility
Extensibility is ensured through the use of modular components:
- **AWS Lambda**: Allows for the addition of new functions and processing steps without disrupting existing workflows. Lambda functions can be easily integrated into the system to handle specific tasks, such as ordering new imagery or processing data.
- **Apache Airflow**: Provides a flexible and powerful platform for orchestrating complex workflows. New processing steps or features can be added to the workflow definition, allowing the system to evolve and adapt to new requirements with minimal disruption.

### Reliability
Reliability is addressed through the use of robust and fault-tolerant components:
- **Amazon SQS (Simple Queue Service)**: Ensures reliable message delivery between components, enabling asynchronous communication and decoupling of system parts. SQS guarantees that messages are delivered at least once, providing resilience against failures.
- **DynamoDB**: Offers fast and reliable access to metadata and structured data. DynamoDB's high availability and fault tolerance ensure that metadata is always accessible, even in the event of hardware failures.
- **EFS and S3**: Provide durable and highly available storage solutions. EFS ensures that file storage is replicated across multiple availability zones, while S3 offers 99.999999999% durability by replicating data across multiple data centers.

### Cost-Effectiveness
Cost-effectiveness is optimized through the use of serverless and managed services:
- **AWS Lambda**: Executes code in response to events, scaling automatically based on the number of incoming requests. This serverless model eliminates the need for provisioning and managing servers, reducing operational costs.
- **AWS Batch**: Manages compute resources for batch processing jobs, scaling resources up or down based on demand. This ensures that resources are used efficiently, avoiding the costs associated with over-provisioning.
- **S3 and EFS**: Offer cost-effective storage solutions with pay-as-you-go pricing models. S3's tiered storage options allow for cost savings by moving infrequently accessed data to lower-cost storage classes.


## Design justification for system design componenets:

1. DynamoDB - 
   Using NoSQL to reterive large dataset on low latency.
   When using NoSQL DB the alternatives are: 
   * MongoDB - document based additional managment overhead
   * Couchbase
   * Cassandra - wide column storage

DynamoDB is a good choise for big data and ETL:

------------------------------------------------

DynamoDB is really good choice cause if its efficency to handle
Large volume of data in very low latency.
It support Auto scaling. 
And is easly integrated to AWS. 
And high avalbilty and durabilty.

1.Scalability:
Amazon DynamoDB stands out due to its fully managed nature, seamless scalability, high performance with low latency, integrated high availability and durability, and deep integration with the AWS ecosystem. Its managed capacity modes and advanced features like DAX, transactional support, and Streams offer a comprehensive solution for modern, high-performance applications.

. Fully Managed Service
No Infrastructure Management: DynamoDB is a fully managed database service, which means AWS handles all aspects of database management, including hardware provisioning, setup, configuration, replication, software patching, and backups.
Operational Simplicity: Reduces operational overhead and complexity, allowing you to focus on application development rather than database management.
2. Seamless Scalability
Automatic Scaling: DynamoDB automatically scales throughput capacity and storage based on demand. You don’t need to manually adjust settings or manage clusters.
Global Tables: Provides multi-region replication with Global Tables, which allows for seamless data distribution and high availability across different geographic locations.
3. High Performance
Single-Digit Millisecond Latency: Designed for high performance with single-digit millisecond response times for read and write operations.
In-Memory Caching: DynamoDB Accelerator (DAX) is an in-memory caching service that provides microsecond response times for cached data, further improving performance.
4. Built-In High Availability and Durability
Multi-AZ Replication: Data is automatically replicated across multiple Availability Zones within a region, ensuring high availability and fault tolerance.
Automated Backups: Provides automated backups and point-in-time recovery to protect against data loss.
5. Flexible Data Model
Key-Value and Document Store: Supports both key-value and document data models, allowing for flexible schema design.
Attributes and Indexes: Supports various types of indexes, including Global Secondary Indexes (GSI) and Local Secondary Indexes (LSI), to optimize query performance.
6. Integrated with AWS Ecosystem
AWS Integration: Seamlessly integrates with other AWS services, such as Lambda for serverless computing, S3 for object storage, and Kinesis for real-time data processing.
Security and Compliance: Offers built-in security features, including encryption at rest and in transit, fine-grained access control with IAM, and compliance with various industry standards.
7. Managed Capacity and Cost Control
Provisioned and On-Demand Capacity Modes: Allows you to choose between provisioned throughput (with predictable pricing) and on-demand capacity (with flexible scaling and cost efficiency).
Cost-Effective: Pricing is based on usage, and you can control costs by configuring read/write capacity units and using features like reserved capacity.
8. Easy to Get Started
Quick Setup: Easy to set up and start using with minimal configuration, thanks to the managed nature of the service.
Developer-Friendly: Supports a variety of SDKs and tools for different programming languages, making it easier for developers to integrate with applications.
9. Advanced Features
Transactional Support: Provides support for ACID transactions to ensure consistency and reliability for complex operations.
Streams: DynamoDB Streams capture changes to items in a table and can be used for real-time processing and event-driven architectures.


2. S3 - blob storage
  Designed for low latency and high throughput.
  * Scalability: S3 excels in scalability, automatically adjusting to handle increasing data volumes and request rates without manual intervention. Its virtually unlimited capacity and high throughput make it suitable for a wide range of applications, from small files to large data lakes. 
  you can store as much as you want .
  Excel in providing reliable and scalble storage for wide range of data types.
  It also offers backup and recovery for resilance.

3. Servless architecture - 

4.


5.PostgreSQL - 



6.Apache Airflow - 


Pipeline workflow:
Client Request: The process begins when a client submits a request through the Client Interface, specifying parameters such as time frame, area of interest, image provider, and type.

Request Handling: The Client Interface forwards the request to the API Gateway.

Request Processing: The API Gateway handles authorization and authentication of the request. It then forwards the request to the Analysis Ready Data Service (ARDS).

Metadata Check: ARDS checks the availability of the requested imagery. It queries MongoDB to verify if the metadata and the state of the requested imagery are available.

Image Availability:

If Imagery is Available: ARDS retrieves the imagery from Amazon EFS. If Imagery is Not Available: ARDS triggers an AWS Lambda function to order new imagery from satellite providers. Imagery Acquisition: The AWS Lambda function processes the order for new imagery. Once the imagery is acquired, Lambda updates ARDS with the new status.

Processing Queue: Upon receiving new imagery, ARDS sends a notification to Amazon SQS to signal that new imagery is available for processing.

Image Processing: EC2 instances retrieve the imagery from Amazon EFS based on notifications from SQS. They execute the image processing tasks using predefined algorithms.

Workflow Orchestration: Apache Airflow manages and orchestrates the image processing workflow, scheduling and monitoring the various processing steps.

Batch Processing: AWS Batch handles large-scale computational tasks, running parallel processing jobs as defined by the processing pipeline.

Results Storage: After processing is complete, the processed imagery and analysis results are stored in Amazon S3 for durable and scalable storage.

Metadata Storage: Metadata related to processed imagery is stored in DynamoDB for quick lookups and efficient querying.

Conclusion
The designed pipeline provides a robust framework for satellite imagery acquisition and analysis. By incorporating a series of well-defined steps, the system ensures efficient handling of client requests, effective management of imagery availability, and comprehensive processing workflows.

Key elements of the system include:

A secure and scalable Client Interface and API Gateway for managing and authenticating requests. Analysis Ready Data Service (ARDS) for checking imagery availability and initiating new orders if necessary. AWS Lambda for managing imagery acquisition and status updates. EC2 instances and AWS Batch for handling large-scale image processing tasks. Amazon S3 and DynamoDB for storing processed imagery and metadata. This structured approach ensures that the system can scale to handle large volumes of data, maintain reliability through effective orchestration and processing, and offer a user-friendly interface for interaction with the processed imagery. The design emphasizes flexibility, scalability, and efficiency, making it well-suited for managing and analyzing satellite imagery.

