# Crawl webapp

## Table of Contents

- [Overview](#overview)
- [Components ](#Components)
- [Configuration and Parameter Validation](#Configuration-and-Parameter-Validation)

## Overview
This Crawler Web App is designed to facilitate and manage the complex operations involved in large-scale web crawling. It provides a reliable framework for handling various aspects of web crawling, including uploading and managing URL lists, processing crawled data, triggering recrawls, and parsing robots.txt files to ensure compliance with website crawling policies.

The project is built to integrate with key technologies like Hadoop for distributed data storage, Accumulo for secure and scalable storage, Oozie for workflow scheduling, and Graphite for real-time metrics monitoring. By leveraging these technologies, this web app guarantees efficiency in processing, storing, and monitoring large amounts of web data.

## Components
This project is composed of several key components that work together to manage the complex workflow of web crawling and data processing. Here's a deeper look at each component and its functionality:
### 1. URL Management
- **URL Upload Servlets:** <br>
These servlets are the entry point for users who need to upload lists of URLs to be crawled. The servlets handle the receipt of these files, performing initial validation to ensure they meet expected formats **(CSV or GZipped CSV)**. This step is essential as it ensures that only correctly formatted and safe files are processed further, which maintain the integrity of the crawling process. Once validated, the files are stored locally in a designated upload directory, before being processed and transferred to HDFS. 
- **URL Upload to HDFS Jobs:** <br>
These are scheduled or triggered jobs that automate the transfer of the validated URL files from the local storage to the **Hadoop Distributed File System (HDFS)**. The jobs are designed to handle large volumes of data efficiently and include logic to only move files that are ready for processing. This component ensures that all URL lists are uploaded to the distributed environment in a timely manner, preparing them for the crawling phase.


### 2. Crawl Data Management

- **Crawl Data Upload:** <br>
Similar to the URL upload process, this component handles the ingestion of crawled data, which includes data files such as URLs, crawled links, internal links, and rss links. After upload, the files are validated and temporarily stored in the local filesystem. The final step involves transferring these files to HDFS, where they can be further processed or analyzed.
- **Crawl Data Processing Jobs:** <br>
These jobs are responsible for the ongoing management of the crawled data within HDFS. This includes creating completion markers (such as `_DONE` files) to indicate that a particular set of data files has been successfully processed. These jobs ensure that the data is correctly cataloged and made available for any subsequent workflows, such as analytics or further data processing.


### 3. Recrawl Management:

- **Recrawl Trigger Servlets:** <br>
These servlets provide an interface for triggering recrawl operations. Users can request recrawls based on various criteria, such as changes in backlinks or updates to a site's crawl index. The servlets orchestrate these requests through **Oozie workflows**, which manage the complexity of scheduling and running the recrawls across a distributed environment.

- **Recrawl Status Monitoring:** <br>
This component provides endpoints that allow users to monitor the progress of their recrawl requests. By querying these endpoints, users can track the status of ongoing jobs, check for completion, and receive notifications if a job fails. This feedback loop is critical for ensuring that recrawl operations are proceeding as expected and allows for quick intervention if something goes wrong.


### 4. Robots.txt Handling

- **Robots.txt Fetching and Parsing:** <br>
    
A crucial component that handles the retrieval of `robots.txt` files from the domains being crawled. These files dictate the rules that crawlers must follow, such as which parts of a site can or cannot be crawled. The fetched files are parsed to extract these rules, which are then stored in **Accumulo**, a distributed key/value store, ensuring that the crawler respects site-specific policies during its operations.

- **Custom Robots.txt Management:** <br>
    
This feature allows for the uploading and management of custom `robots.txt` files. These custom files can override the standard rules fetched from websites, enabling more granular control over the crawling process. This is particularly useful in scenarios where default crawling rules are too restrictive or need to be adjusted for specific crawling campaigns.
    


### 5. Metrics and Monitoring
- **Graphite Integration:** <br>
The system is tightly integrated with Graphite, a monitoring and metrics tool. Through this integration, the system continuously reports key performance indicators (KPIs) such as request rates, processing times, and system health metrics. These real-time insights are crucial for maintaining operational stability and for proactively addressing any issues that may arise during the crawling and data processing workflows. The metrics provide visibility into the system’s performance, enabling optimization and ensuring that the infrastructure can scale with the demands placed upon it.


## Configuration and Parameter Validation
- **Configuration** <br>
The application has a Configuration file that provides methods to load and get different settings, which is key in setting up or controlling this web application environment. The configuration file manages configurations for key components 
like **Hadoop**, **Accumulo**, and **Oozie** by loading settings from various XML files and managing paths and directories needed for data uploads and processing. also sets up the Oozie client, and includes application-specific settings like job tracker paths, metrics, and blacklisted queries. Additionally, it comes with helper functions for URLs and time formatting, as well as creating a **Graphite prefix based on the hostname**.
- **Parameter Validation** <br>
The domain and mode parameters in HTTP requests are valid to ensure that only valid requests are processed. It checks if **domain**  is present and if **mode**  is either **r**  or **a**. If validation fails, it sends an HTTP 500 error and throws a **ServletException**, helping to enforce proper parameter usage in the web application.

