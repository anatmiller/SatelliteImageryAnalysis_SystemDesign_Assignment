
## Satellite Image acquiry and analysis System Design 


## [API](API.md#renderthumbnail)


Design is Scalable, Extensible, and Efficient System for Satellite Imagery Acquisition and Analysis

------------------------


## Introduction
This document outlines the design of a robust, scalable, extensible and efficent system that aims to acquire satellite imagery from various sources and perform analysis tasks on the acquired data. The system is designed to be scalable, extensible, and efficient, taking into consideration real-world constraints such as limited bandwidth and storage capacity.


# Step 1: Use Cases and Constraints

The system is designed to handle the following use cases and constraints:

## The problem handle only the following use cases:

1. **Acquire Satellite Imagery**:
    - Connects to multiple satellite imagery providers.
    - Retrieves images based on parameters like area of interest, time frame, and resolution.
    - Stores raw imagery in scalable storage.

2. **Define and Execute Analysis Pipelines**:
    - Users define a sequence of processing steps (e.g., vegetation index calculation).
    - Executes the defined processing pipeline on the acquired imagery.
    - Stores and provides access to the analysis results.

3. **Retrieve Processed Results**:
    - Users query and retrieve processed images and analysis outputs.
    - Provides access to results through an API or user interface.

4. **System has high scalability**.

5. **System is extensible**.

6. **System is very efficient**.


### Constraints and assumptions

### Constraints
1. **Data Volume and Storage**: The system should efficiently handle large volumes of high-resolution imagery and store it.

2. **Processing Time**: The system should execute analysis pipelines within acceptable time frames to provide timely results.

3. **Integration Flexibility**: The system must seamlessly integrate with various data formats and APIs from different satellite image providers.

4. **Scalability**: The system should easily scale horizontally to handle varying loads and high processing demands.

5. **Security**: The system must prioritize data security and comply with relevant privacy regulations.

6. **Efficiency**: The system should optimize resource utilization and minimize processing time to achieve efficient performance.

### Assumptions
The system is designed with the following assumptions:

1. **Data Acquisition**: The system will be able to acquire satellite imagery from multiple providers, regardless of the data formats used by each provider.

2. **Processing Pipelines**: Users will have the flexibility to define custom processing pipelines by selecting from a set of predefined steps.

3. **Resource Management**: The system will dynamically scale its computational and storage resources based on the demand and workload.

4. **Data Quality**: The acquired imagery from different providers will meet the agreed-upon quality standards to ensure accurate analysis results.

5. **User Interaction**: Users will have a user-friendly API or interface to interact with the system. They will be able to submit requests and retrieve results easily.


# # Step 2: High level design

![Imgur](http://i.imgur.com/xjdAAUv.png)



## Step 3: Design core components

Components
Client Interface: This is the entry point for client requests. Clients initiate requests to process satellite imagery by specifying parameters such as time frame, area of interest, image provider, and type. These requests are sent to the API Gateway.

API Gateway: This component manages and secures client requests by handling authorization, authentication, and routing. It receives requests from clients and forwards them to the Analysis Ready Data Service (ARDS) or triggers a Lambda function for image acquisition. The API Gateway is designed to provide a secure and scalable interface, handling a large volume of requests with low latency.

Analysis Ready Data Service (ARDS): This service handles requests related to metadata and the state of satellite imagery. It checks if the requested imagery is available, manages area of interest administration, and handles ad-hoc requests. ARDS queries MongoDB for metadata and request states. If imagery is available, it retrieves it for processing. If imagery is not available, ARDS triggers a Lambda function to order new imagery. ARDS centralizes the management of metadata and request states, facilitating efficient image retrieval and request handling.

AWS Lambda: This service executes code in response to triggers, such as ordering new imagery when requested imagery is not available. It is triggered by the API Gateway, orders imagery from satellite providers, updates ARDS, and monitors the status of imagery acquisition. Lambda offers a serverless, on-demand execution environment, which is efficient and cost-effective for handling image ordering and status updates.

Amazon Elastic File System (EFS): This component provides scalable and shared storage for satellite imagery, accessible by multiple EC2 instances. EFS stores acquired imagery, making it available for processing by EC2 instances. EFS is essential for handling large volumes of imagery and provides scalable concurrent access to file storage.

Amazon SQS (Simple Queue Service): This service facilitates message-based communication for event-driven processing. It signals when new imagery is available for processing. SQS receives events from the system, such as when new imagery is stored in EFS, and triggers image processing workflows. SQS enables decoupling of components, allowing asynchronous communication and reliable message delivery.

EC2 Instances: These instances perform image processing tasks by retrieving imagery from EFS, executing processing algorithms, and handling large-scale computation. EC2 instances pull imagery from EFS based on SQS events and process it according to the defined pipeline. EC2 provides scalable computing resources, allowing for flexible and cost-effective management of processing loads.

Apache Airflow: This tool manages and orchestrates complex workflows for image processing, including scheduling and monitoring tasks. It coordinates the execution of processing steps and ensures that tasks are executed in the correct sequence. Apache Airflow provides a powerful platform for managing workflows, which is essential for orchestrating various processing steps.

AWS Batch: This service manages and executes batch processing jobs for handling large-scale computational tasks. It executes parallel processing jobs defined by the processing pipeline and scales resources as needed based on job requirements. AWS Batch offers efficient, scalable, and cost-effective batch processing by automating compute resource management and job execution.

Amazon S3: This component stores processed imagery and analysis results. It receives and stores output from image processing tasks and provides durable and scalable storage for results. S3 offers high durability, scalability, and availability, making it ideal for storing large volumes of processed data.

DynamoDB: This service stores metadata and structured results for quick lookups and efficient querying. It stores and retrieves metadata related to processed imagery, providing fast and predictable performance with seamless scalability.

Design Justification
----------------------------
Scalability is achieved through components like EC2, EFS, S3, and AWS Batch, which are designed to scale horizontally, handling large volumes of imagery and processing tasks efficiently. Extensibility is ensured through modular components, such as Lambda for ordering imagery and Apache Airflow for orchestration, allowing new features or processing steps to be added with minimal disruption. Reliability is addressed by SQS and DynamoDB, which provide reliable message delivery and quick metadata access, while EFS and S3 ensure durable and accessible data storage. Cost-effectiveness is optimized through serverless components like Lambda and managed services like AWS Batch, which scale resources based on demand and avoid over-provisioning.

## Pipeline workflow:

Client Request: The process begins when a client submits a request through the Client Interface, specifying parameters such as time frame, area of interest, image provider, and type.

Request Handling: The Client Interface forwards the request to the API Gateway.

Request Processing: The API Gateway handles authorization and authentication of the request. It then forwards the request to the Analysis Ready Data Service (ARDS).

Metadata Check: ARDS checks the availability of the requested imagery. It queries MongoDB to verify if the metadata and the state of the requested imagery are available.

Image Availability:

If Imagery is Available: ARDS retrieves the imagery from Amazon EFS.
If Imagery is Not Available: ARDS triggers an AWS Lambda function to order new imagery from satellite providers.
Imagery Acquisition: The AWS Lambda function processes the order for new imagery. Once the imagery is acquired, Lambda updates ARDS with the new status.

Processing Queue: Upon receiving new imagery, ARDS sends a notification to Amazon SQS to signal that new imagery is available for processing.

Image Processing: EC2 instances retrieve the imagery from Amazon EFS based on notifications from SQS. They execute the image processing tasks using predefined algorithms.

Workflow Orchestration: Apache Airflow manages and orchestrates the image processing workflow, scheduling and monitoring the various processing steps.

Batch Processing: AWS Batch handles large-scale computational tasks, running parallel processing jobs as defined by the processing pipeline.

Results Storage: After processing is complete, the processed imagery and analysis results are stored in Amazon S3 for durable and scalable storage.

Metadata Storage: Metadata related to processed imagery is stored in DynamoDB for quick lookups and efficient querying.


## Conclusion
The designed pipeline provides a robust framework for satellite imagery acquisition and analysis. By incorporating a series of well-defined steps, the system ensures efficient handling of client requests, effective management of imagery availability, and comprehensive processing workflows.

Key elements of the system include:

A secure and scalable Client Interface and API Gateway for managing and authenticating requests.
Analysis Ready Data Service (ARDS) for checking imagery availability and initiating new orders if necessary.
AWS Lambda for managing imagery acquisition and status updates.
EC2 instances and AWS Batch for handling large-scale image processing tasks.
Amazon S3 and DynamoDB for storing processed imagery and metadata.
This structured approach ensures that the system can scale to handle large volumes of data, maintain reliability through effective orchestration and processing, and offer a user-friendly interface for interaction with the processed imagery. The design emphasizes flexibility, scalability, and efficiency, making it well-suited for managing and analyzing satellite imagery.

Assignee : Anat Miller 



