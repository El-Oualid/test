# Web Crawler Management System

This project is a web-based management system designed to handle and monitor web crawling tasks. It includes various servlets and background jobs that facilitate the uploading of URLs, managing crawled data, interacting with Hadoop Distributed File System (HDFS), and reporting metrics using Graphite.

## Table of Contents

- [Project Structure](#project-structure)
- [Setup](#setup)
- [Usage](#usage)
- [Configuration](#configuration)
- [License](#license)

## Project Structure

The project is organized into several key components, each responsible for different aspects of the web crawling management process:

- **CrawlJobWebappConfig.java**: Handles the configuration of the web application, including Hadoop and Accumulo configurations, as well as context lookups for various settings.
- **CrawlUploadServlet.java**: Manages the uploading of crawl data files to the local server, validating and renaming them before processing.
- **CrawlWorkchunkDownloadServlet.java**: Provides functionality to download work chunks from HDFS, with optional on-the-fly decompression.
- **CrawlWorkchunkServletV2.java**: Similar to the above, but modified to handle a new directory structure and compressed files.
- **CreateDoneMarkersJob.java**: A Quartz job that creates marker files in HDFS to signal the completion of crawling tasks.
- **FetchRobotsTxtServlet.java**: Fetches and processes the `robots.txt` files from specified domains, storing the results in Accumulo.
- **ParamValidator.java**: A utility class for validating request parameters, ensuring they meet the expected criteria.
- **RecrawlStatusServlet.java**: Retrieves and displays the status of recrawl jobs from the Oozie workflow engine.
- **RobotsTxtServlet.java**: Manages the storage and retrieval of `robots.txt` directives in Accumulo, with support for both GET and POST operations.
- **TriggerBacklinksDomainRecrawlSubmit.java**: Triggers a recrawl job for specified domains based on their backlinks, submitting tasks to the Hadoop cluster.
- **TriggerCrawlindexDomainRecrawlSubmit.java**: Triggers a recrawl job for specified domains based on their crawl index, similar to the backlinks trigger.
- **UploadToHDFSJob.java**: A Quartz job that uploads processed crawl data from the local server to HDFS.
- **UrlUploadCrawlindexServlet.java**: Handles the upload of URL files specifically for crawl index operations, validating and storing them locally.
- **UrlUploadCrawlindexToHDFS.java**: A Quartz job that moves URL files from the local server to HDFS, specifically for crawl index operations.
- **UrlUploadServlet.java**: Manages the uploading of URL files to the local server, similar to `CrawlUploadServlet`, but specifically for URLs.
- **UrlUploadToHDFS.java**: A Quartz job that uploads URL files from the local server to HDFS.

## Setup

### Prerequisites

- Java 8 or higher
- Apache Hadoop
- Apache Accumulo
- Apache Oozie
- Apache Tomcat (or any other servlet container)
- Quartz Scheduler
- Graphite for metrics reporting

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/web-crawler-management-system.git
   cd web-crawler-management-system
