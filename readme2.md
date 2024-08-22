# Web Crawler Management System
## Components
### 1. Configuration and Parameter Validation
- **Configuration** <br>
The application has a Configuration file that provides methods to load and get different settings, which is key in setting up or controlling this web application environment. The configuration file manages configurations for key components 
like **Hadoop**, **Accumulo**, and **Oozie** by loading settings from various XML files and managing paths and directories needed for data uploads and processing. also sets up the Oozie client, and includes application-specific settings like job tracker paths, metrics, and blacklisted queries. Additionally, it comes with helper functions for URLs and time formatting, as well as creating a **Graphite prefix based on the hostname**.
- **Parameter Validation** <br>
The domain and mode parameters in HTTP requests are valid to ensure that only valid requests are processed. It checks if **domain**  is present and if **mode**  is either **r**  or **a**. If validation fails, it sends an HTTP 500 error and throws a **ServletException**, helping to enforce proper parameter usage in the web application.
