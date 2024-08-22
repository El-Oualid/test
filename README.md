# Crawl webapp

## Table of Contents

- [Overview](#overview)
- [Components ](#Components)
- [How the Components Work Together](#how-the-components-work-together)

## Overview
Overview of all the application
(I am writing this readme file using this logic :
1. **Configuration and Validation:** CrawlJobWebappConfig.java ParamValidator.java
2. **Servlets Handling Uploads and Downloads:**  CrawlUploadServlet.java  CrawlWorkchunkDownloadServlet.java CrawlWorkchunkServletV2.java UrlUploadCrawlindexServlet.java UrlUploadServlet.java
3. **Robots.txt Handling:** FetchRobotsTxtServlet.java RobotsTxtServlet.java
4. **Job and Status Handling:** CreateDoneMarkersJob.java RecrawlStatusServlet.java UploadToHDFSJob.java
5. **URL and HDFS Uploads:** UploadToHDFSJob.java UploadToHDFSServlet.java UrlUploadToHDFS.java 
6. **Triggering Crawls and Recrawls:** TriggerBacklinksDomainRecrawlSubmit.java TriggerCrawlindexDomainRecrawlSubmit.java )

## Components
### 1. Configuration and Parameter Validation
- **Configuration** <br>
The application has a Configuration file that provides methods to load and get different settings, which is key in setting up or controlling this web application environment. The configuration file manages configurations for key components 
like **Hadoop**, **Accumulo**, and **Oozie** by loading settings from various XML files and managing paths and directories needed for data uploads and processing. also sets up the Oozie client, and includes application-specific settings like job tracker paths, metrics, and blacklisted queries. Additionally, it comes with helper functions for URLs and time formatting, as well as creating a **Graphite prefix based on the hostname**.
- **Parameter Validation** <br>
The domain and mode parameters in HTTP requests are valid to ensure that only valid requests are processed. It checks if **domain**  is present and if **mode**  is either **r**  or **a**. If validation fails, it sends an HTTP 500 error and throws a **ServletException**, helping to enforce proper parameter usage in the web application.


### 2. Servlets Handling Uploads and Downloads
In the context of Java web development, servlets are a class in Java that extends the functionality of a server when hosting applications accessed by a request-response programming model. It is mostly used for creating dynamic content in web pages and for handling web requests and responses. Servlets have a set lifecycle: they start with the init() method when first loaded, handle client requests using the service() method (which calls `doGet()`, `doPost()`, `doDelete()` etc., depending on the request), and finish by using the destroy() method to clean up when they're no longer needed.
- **Upload Servlets:** <br>
These servlets take care of posting the files through the `doPost()` method. Making sure that the files are validated, renamed, and stored while tracking relevant metrics. Every servlet is focused on a certain kind of files, such as general URLs or crawl index data.
- **Download Servlets:** <br>
These servlets are actually intended to retrieve and process files from HDFS  via the `doGet()` method. Specifically handling "work chunks" related to web crawling. They ensure secure and efficient file downloading, including support for decompression, as well as collecting and reporting detailed metrics regarding file retrieval and processing activities.


### 3. Robots.txt Handling:

- **Robots.txt Fetching:** <br>
In our app, fetching `robots.txt` files is a critical task that ensures we respect the rules set by website owners. This involves retrieving the `robots.txt` files for each domain, reading through them to identify the crawling directives, and then storing this information in a database. By doing this, we make sure our crawler knows exactly what it can and cannot do on each site, helping to prevent any potential issues like overwhelming a site with too many requests or accessing restricted areas.

- **Robots.txt Management** <br>
Once we have the `robots.txt` data stored, managing and querying this information becomes the next important step. This functionality allows us to easily get the existing rules for any domain, make updates like adjusting crawl limits, or even apply custom directives when required. This management process is crucial for keeping our system flexible and responsive, ensuring we can quickly adapt to any changes or special requirements in our web crawling operations.

Together, these functions ensure that our handling of `robots.txt` files is both efficient and effective, from initial retrieval to ongoing management.


### 4. Job and Status Handling:

- **Marking Task Completion**
    
    This ensures that the crawling and data processing tasks are officially marked as completed by creating `_DONE` marker directories in Hadoop's HDFS. It also generates necessary placeholder files if the actual data is unavailable, allowing downstream systems to recognize that the processing for specific days is finalized and can proceed without interruption. This mechanism ensures that the previous steps have been "completed" in some form.
    
- **Job Status (Monitoring and Reporting)**
    
    This aspect is able to track and report the status of data processing jobs. It retrieves the current status of specific jobs from the Oozie workflow system, such as whether they are running, succeeded, or failed, and provides this status to clients or systems that request it. It also logs and tracks metrics related to job status requests, such as the total number of requests and processing time, using a Graphite metrics system. This ensures that the health and performance of the job management system can be monitored, keeping track of job progress and ensuring that any issues are promptly identified and addressed.
    
- **Automating Data Upload to HDFS**

    This component, a scheduled job, automates the identification and transfer of relevant data files from a local directory to Hadoop's HDFS. It checks for files related to web crawling (`crawled-urls`, `crawled-links`, `internal-links`, `rss-links`), verifies if they are old enough to be processed, and then moves these files to `toHdfsDir`. The job also handles the transfer of any related archived files within HDFS. It uses a thread pool to efficiently upload these files to HDFS, ensuring that all required files are transferred and logged appropriately. This functionality is essential for maintaining the flow of data from local storage to a scalable distributed environment.<br>
    <br>There another scheduled job that specifically handles the upload of URL-related files to HDFS. It scans a local directory for files that match certain patterns, ensures they are appropriately named (with the correct .gz extension for Gzip files), and checks if they are old enough before moving them to a toHdfsDir. These files are then uploaded to HDFS using a thread pool for concurrent operations.


### 5. Triggering Crawls and Recrawls:
- We added two servlets who are responsible for triggering domain recrawling processes in a web application. Handling HTTP GET requests, validate domain names, and check for any blacklisted domains. If the domains are valid and not blacklisted, they put a code to configure and submit jobs using Hadoop and Apache Accumulo to perform the recrawl.  For better performance optimization, they utilize caching to store the results of previous recrawl requests, avoiding redundant processing. Additionally, they track and report metrics related to the processing of these requests, and handle any errors by logging them and communicating issues back to the client.<br>

- Each of these servlets specializes in a particular recrawling activity. One servlet is designed to focus on the recrawl of backlinks, which are external links pointing to the domain, while the other focuses on the recrawl of the main content or index pages of the domain. Both servlets use distinct caching mechanisms and job configurations to ensure they effectively fulfill their respective tasks within the application.
