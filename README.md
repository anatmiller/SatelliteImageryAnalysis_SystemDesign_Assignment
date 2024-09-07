
## Satellite Image acquiry and analysis System Design 

# [![Build Status](https://travis-ci.org/mapbox/geojson-thumbnail.svg?branch=master)](https://travis-ci.org/mapbox/geojson-thumbnail) [![Coverage Status](https://coveralls.io/repos/github/mapbox/geojson-thumbnail/badge.svg?branch=master)](https://coveralls.io/github/mapbox/geojson-thumbnail?branch=master)

```
npm install -g @mapbox/geojson-thumbnail
npm link

export MapboxAccessToken=<your token>
geojson-thumbnail <your-geojson-file> thumb.png
```

![nmatsutbwh](https://user-images.githubusercontent.com/1288339/35072800-247f4dfc-fbb4-11e7-8141-b1abe76125f8.gif)

## [API](API.md#renderthumbnail)

`geojson-thumbnail` exposes an API to render your own custom thumbnails of GeoJSON features.


Design is Scalable, Extensible, and Efficient System for Satellite Imagery Acquisition and Analysis

------------------------


## Introduction
This document outlines the design of a robust, scalable, extensible and efficent system that aims to acquire satellite imagery from various sources and perform analysis tasks on the acquired data. The system is designed to be scalable, extensible, and efficient, taking into consideration real-world constraints such as limited bandwidth and storage capacity.


# # Step 1: Use Cases and Constraints

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

## Core Component Details

1. **Client Interface:**:
    - Implement a data ingestion module to fetch satellite imagery from different sources.
    - Validate the acquired data to ensure its integrity and quality.
    - Store the acquired data in a distributed storage system for efficient retrieval.

2. **Data Processing**:
    - Design a distributed processing framework to handle the parallel processing of the acquired imagery.
    - Implement scalable algorithms for various analysis tasks, such as image classification, object detection, and change detection.
    - Utilize distributed computing resources to optimize the processing time.

3. **Analysis Pipelines**:
    - Develop modular and extensible pipelines for different analysis tasks.
    - Implement algorithms and models specific to each analysis task.
    - Integrate the pipelines with the data processing component for efficient processing.

4. **Data Storage**:
    - Design a distributed storage system to handle the large volume of acquired imagery and analysis results.
    - Utilize efficient storage mechanisms, such as distributed file systems or object storage.
    - Implement indexing and querying mechanisms for efficient retrieval of data.

5. **User Interface**:
    - Develop a user-friendly web-based interface for users to interact with the system.
    - Implement search functionality to allow users to search for relevant imagery and analysis results.
    - Provide visualization capabilities to display the satellite imagery and analysis results in an intuitive manner.


## Pipeline workflow:

## Conclusion
The designed system provides a scalable, extensible, and efficient solution for satellite imagery acquisition and analysis. It takes into consideration real-world constraints and offers a user-friendly interface for users to interact with the system. With its modular design and distributed processing capabilities, the system is well-equipped to handle large volumes of data and perform various analysis tasks on satellite imagery.

Assignee : Anat Miller 



