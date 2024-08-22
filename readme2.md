# Web Crawler Management System

## Table of Contents

- [Overview](#overview)
- [Components](#components)
- [How the Components Work Together](#how-the-components-work-together)
- [Setup](#setup)
- [Usage](#usage)
- [Configuration](#configuration)
- [License](#license)

## Overview

The Web Crawler Management System is designed to facilitate the management, monitoring, and execution of large-scale web crawling tasks. This system provides the necessary tools for uploading and managing URL lists, fetching and processing `robots.txt` files, triggering recrawl jobs, and handling the storage and retrieval of crawled data in a Hadoop-based environment. The system also includes robust logging and metrics reporting through Graphite, ensuring visibility into the crawling operations.

## Components

### URL Management

- **URL Upload Servlets**: These servlets handle the upload of URL files. Users can submit URL lists, which are validated and temporarily stored on the local server before being processed or transferred to HDFS (Hadoop Distributed File System).
  
- **URL Upload to HDFS Jobs**: Background jobs that monitor the local upload directories, validate file integrity, and transfer the URL files from the local storage to the appropriate directory in HDFS for further processing.

### Crawl Data Management

- **Crawl Data Upload**: Similar to the URL management, this component handles the uploading of crawled data (such as crawled URLs and links) to the server. After validation, these files are stored locally and later transferred to HDFS.

- **Crawl Data Processing Jobs**: These jobs handle the movement of validated crawled data files from local storage to HDFS. They also manage the creation of markers in HDFS that signal the completion of data processing tasks.

### Recrawl Triggering

- **Recrawl Trigger Servlets**: These servlets allow the triggering of recrawl operations based on specified conditions, such as changes in backlinks or crawl indexes. They initiate Hadoop jobs through Oozie workflows, which prioritize and schedule the recrawl tasks.

- **Recrawl Status Monitoring**: Provides endpoints to monitor the status of the recrawl jobs. Users can query the system to get real-time status updates on the initiated recrawl operations.

### Robots.txt Handling

- **Robots.txt Fetching and Parsing**: This component fetches `robots.txt` files from specified domains, parses the rules, and stores them in an Accumulo database. The parsed rules determine the crawling permissions for each domain.

- **Custom Robots.txt Management**: Users can upload custom `robots.txt` files that override the standard rules. These custom rules are also stored and managed within the system.

### Metrics and Monitoring

- **Graphite Integration**: The system is integrated with Graphite for real-time metrics reporting. This includes tracking of request rates, processing times, and other key performance indicators (KPIs) across various components.

## How the Components Work Together

The Web Crawler Management System is designed to streamline the process of managing web crawling tasks. Here's how the components interact:

1. **URL Uploading**: Users upload URL files via the provided servlets. These files are stored locally and validated before being moved to HDFS by the scheduled background jobs.

2. **Crawl Data Handling**: As the web crawling tasks generate data, this data is uploaded to the system in a similar manner to the URL files. The system ensures that the data is properly stored and made available in HDFS for further processing.

3. **Recrawl Triggering**: Based on the changes detected in backlinks or crawl indexes, users can trigger recrawl jobs through the dedicated servlets. These jobs are managed by Hadoop and orchestrated using Oozie workflows.

4. **Robots.txt Management**: The system fetches and processes `robots.txt` files to determine which URLs should be crawled or ignored. Custom rules can be uploaded and applied as needed.

5. **Monitoring and Reporting**: Throughout the process, the system tracks various metrics and reports them to Graphite, allowing administrators to monitor the health and performance of the crawling operations in real time.

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
