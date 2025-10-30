# Oracle Cloud Infrastructure Observability

## Observability Overview and its Pillars

- Traditional Monitoring

    - Collecting System Metrics and Logs
    - Track errors and exceptions
    - Store metrics to later analyze
    - React to incidents / alerts
    - Challenges
        - Correlations
        - What POST/PUT actions are run by API endpoint and why
        - Trend of data transfer between points
        - Check for unusual data transfers
        - Latency analysis
        - Network and Deployment issues

- Observability : Ability to understand a system's internal state by analysing the data about what's happening in context aross environments
    
    - Observability Pillars
        - Metrics
        - Logs
        - Traces
    
    - Purposes
        - Enhance visibility in cloud environments
        - Identifying trends and outliers (occurs rarely)
        - Instrumentation, correlation and computation
        - Identifying potential bottlenecks and patterns
        - Insights to take actions and prevent errors 

- Observability and Management Services

    - Monitoring Service: Collects, aggregates, and analyzes metrics from OCI resources to provide visibility into performance and availability.
    - Logging Service: Collects, aggregates, and analyzes log data from OCI resources to help with troubleshooting and auditing.
    - Events Service: Automatically responds to events in your OCI environment by triggering a variety of actions based on those events.
    - Log Analytics: Analyzes log data from diverse sources, including OCI and on-premises systems, to provide insights and troubleshooting capabilities.
    - Application Performance Monitoring (APM): Offers solutions for application performance and observability through distributed tracing, helping to identify and resolve performance bottlenecks.
    - Stack Monitoring: Provides a simple way to discover and monitor Oracle applications and their underlying infrastructure components.
    - Database Management Service: Offers performance and management for Oracle databases, both cloud and on-premises.
    - Operations Insights: Uses machine learning to analyze metrics and logs, providing insights into performance issues and capacity planning. 

## Monitoring service
- Active monitoring :  It uses synthetically generated data or simulated user interactions (synthetic monitoring) to check if services are available and performing as expected, even when there is no real user traffic. This helps identify potential issues before they impact actual users.
    - Example : OCI Health Checks service : It regularly sends HTTP or ping requests from various geographic vantage points to publicly facing endpoints (like websites or APIs) to check their availability and latency.

- Passive monitoring : involves observing and analyzing actual user interactions and real system traffic as it flows through the network and applications
    - Real Data Collection: It collects real-time performance and health metrics emitted automatically by OCI resources and services (like CPU utilization, memory usage, and network throughput).

- Monitoring capabilities

    - Monitoring by service tiers : OCI Monitoring provides out-of-the-box, fine-grained metrics for a wide range of OCI services (e.g., Compute, Networking, Storage, Databases) without requiring any setup. Metrics are automatically emitted from these services (known as service metrics), enabling immediate monitoring of the underlying infrastructure and platform tiers. For example, OCI Compute automatically posts CPU utilization metrics for instances.
        
        - Compartments : Group of resources in OCI tenancy

        - Metric namespaces (oci_blockstore, oci_computeagent) : Logical container for metrics 
       
        - Filter by Dimensions(compute Shape, size etc) and time period (last 24 hours, last 7 days etc)
       
        - Data aggregation with Interval(5m,10m,15m,30m etc) and Statistic (Mean, Max, Min, Sum, Count, Percentile). Resolution is time window between time windows . Interval control length of windows, resolution control start time of each time window. Default : Interval = Resolution
       
        - Metric streams (individual metric data ) and grouping (group by tags, shape, size and type)
    
    - Complex analysis through MQL : OCI Monitoring supports a powerful and flexible Monitoring Query Language (MQL), which allows users to perform complex aggregations, transformations, and filtering of metric data. MQL can be used in the Metrics Explorer for deep analysis and in OCI Alarms to define sophisticated conditions for notification, providing advanced analytical capabilities beyond basic threshold checks.
        
        - Metric Query Components  : METRIC EXPLORER

            - basis of a metric query in OCI Monitoring service : Metric, Interval and Statistics
            
            - Metric: This specifies the particular metric you want to query, such as CpuUtilization, NetworkIn, or DiskReadBytes. It identifies the type of data being measured.
           
            - Interval: The interval defines the time window over which the metric data is aggregated. Common intervals include 1m (1 minute), 5m (5 minutes), 1h (1 hour), or 24h (24 hours). This determines the granularity of the retrieved data points.
           
            - Dimensions: Dimensions are key-value pairs that provide context to a metric. They allow you to filter and segment metric data based on specific attributes of your resources. Examples include resourceId, instanceName, availabilityDomain, or functionName.
           
            - Grouping: The grouping clause allows you to aggregate metric data based on one or more dimensions. This enables you to see the average, sum, or other statistical calculations for a metric across different groups of resources. For example, grouping by availabilityDomain would show the average CPU utilization for all instances within each availability domain.
            
            - Statistic (stats): The stats function applies a statistical aggregation to the metric data within each interval. Common statistics include mean (average), sum, min, max, count, and rate. This determines how the raw data points are summarized for each interval.
           
            - Comparison Operations (comparision operation): These operations are used within MQL queries, particularly when defining alarms, to compare a metric's value against a threshold. Operators like >, <, >=, <=, =, and != are used to define conditions that trigger alerts.

            - Syntax : ```metric [interval] {dimension}.statistic operator value . groupingfunction . statstic``` , ```metric [interval] {dimension}.groupingfunction.statistic operator value . groupingfunction . statstic```
    
    - Transfer metrics using Connector Hub :  The OCI Service Connector Hub can be used to manage and move data, including metrics, between OCI services and to third-party tools. This allows you to transfer metrics from the Monitoring service to targets like Object Storage for long-term archival, Streaming for real-time processing, or to OCI Functions for custom processing or integration with external systems.
    
    - Integration with observability services : OCI Monitoring is a core component of the broader OCI Observability and Management Platform and integrates seamlessly with other OCI observability services such as:
       
        - Notifications: To send real-time alerts via email, Slack, PagerDuty, or HTTPS endpoints when alarms are triggered.
       
        - Logging and Logging Analytics: To correlate metric data with logs for faster troubleshooting and in-depth analysis using machine learning.
       
        - Application Performance Monitoring (APM): To provide full-stack visibility with capabilities like real user monitoring, synthetic monitoring, and distributed tracing.
       
        - Operations Insights: For capacity planning and performance forecasting using machine learning analytics.
    
    - Extensible monitoring with custom metrics : Users can publish their own custom metrics to the Monitoring service using the Monitoring API, SDKs, or the CLI. This enables monitoring of specific application-level data or third-party resources alongside the standard OCI service metrics, providing a unified monitoring experience in the OCI Console's Metrics Explorer. 


- Alarms and Notification services/Streaming services
    - Alarms :  You can create alarms based on specific metric thresholds or the absence of a metric. When an alarm is triggered, it changes state (e.g., from OK to FIRING) and sends a message to a configured destination via the Notifications service.
    - Publish - Subscribe pattern
        - Topics and Subscriptions as channels. When a message is published to a topic, it is delivered to all subscribers of that topic. Retry delivery duration is confiured in when no acknowledgement is received from the subscriber.
    - Alarm query must specify the metric, statistic, interval and trigger threshold.
    - Alarm states (OK, FIRING, RESET, REPEAT)
    - Supression : Avoiding publishing alerts during maintenance or when an issue is resolved.
    - Notification : The highly available, low-latency Notifications service (a pub/sub service) is the primary method for delivering alerts. It uses topics and subscriptions to send messages to various endpoints/Topic, including:
        - Email
        - SMS
        - PagerDuty
        - Slack
        - Custom HTTPS endpoints (Custom URLs)
        - Oracle Functions (for automated remediation) 
    - Streaming Service Integration : OCI Streaming service is a real-time event streaming platform. Alarms can be configured to send messages directly to a stream as a destination, which is useful for situations expecting a high volume of messages or for integration with real-time data processing pipelines or third-party Kafka tools.
    - The OCI Service Connector Hub : provides a centralized place to manage and monitor data movement, allowing you to move data from Monitoring to Streaming, or from Streaming to other services like Object Storage or Functions. 

- Single Plane of glass with Dashboards
    - OCI Dashboards service is a critical component for creating a single, unified view of your entire cloud environment.
    - Customization: You can build custom dashboards using various widget types (line charts, bar charts, tables, etc.) to visualize metrics, track costs, and display logs. This allows you to create a personalized view tailored to specific roles (DevOps, IT operations, finance) and use cases.

- Accessing Metrics and Alarm Data via 
    - Console : The OCI Console provides a user-friendly interface to visualize and analyze metrics.Navigate to Solutions and Platform > Monitoring > Metric Explorer to select metric namespaces, metrics, and dimensions for charting time-series data. Alarms can be created and managed directly within the Console, setting thresholds and defining notification destinations.

    - API : The OCI Monitoring API offers programmatic access to retrieve metric data and manage alarms.
        - This allows for integration with custom applications and automation scripts.
        - Use the API to query metrics, create, update, or delete alarms, and configure alarm notifications.

    - CLI : The OCI Command Line Interface (CLI) provides a command-line tool for interacting with OCI services, including Monitoring. Use CLI commands to retrieve metric data, list alarms, get alarm details, and perform other monitoring-related operations.

    - Monitoring tools like Grafana: OCI Monitoring integrates with external monitoring tools like Grafana via the OCI Metrics plugin for Grafana. This plugin allows Grafana to query the OCI Monitoring service and visualize metrics alongside data from other sources. After installing the plugin and configuring authentication (e.g., Instance Principal or OCI API Key), users can build custom dashboards in Grafana to monitor OCI resources.

    - Metric Explorer : Basic and Advanced Mode

    - IAM Policies and Access
        - Allow group **Alarm-Viewers**  to  *view alarms* :  *read alarm* in tenancy and *read metrics* in tenancy.
        - Allow group **Alarm-Admins**  to  *create and manage metrics* :  *manage alarm* in tenancy , *read metrics* in tenancy and *manage ons-topics* in tenancy.
        - Allow group **Metric-Readers**  to  *view and retrive metrics* :  *read metrics* in compartment ABC where *target.metrics.namespace='oci_blockstore'*
        - Allow group **Metric-Admins**  to  *create and manage metrics* :  *manage metrics* in compartment ABC
        - Allow group **Metric-Viewers**  to  *only view metrics* :    *inspect metrics* in compartment ABC
        - Allow group **Metric-Developers**  to  *publsih custom metrics* :    *use metrics* in tenancy  where *target.metrics.namespace='customnamespace'*

    - Limits
        - Storage Limits : Metric definition stored up to 90 days
        - Alarm Limits : Alarm history stored up to 90 days
        - Metric posted by services : Unlimited
        - Limits to metric streams

## Logging service


### Collect and Manage
- Log Types
    - OCI Service Logs : Provide diagnostic information for specific OCI services. 
    - OCI Audit Logs : Track user-initiated actions within OCI for security, auditing, and compliance
    - Custom Logs :  Ingest and manage log data from sources outside of OCI's built-in services.

- Log groups : logical containers for organizing and managing logs , allowing you to apply specific IAM policies for access control. This is useful for isolating sensitive logs, such as those containing personally identifiable information (PII). 

- Service Log Category : Services provide log categories for the different types of logs available for resources. For example, 
    - the Object Storage service supports the following log categories for storage buckets: read and write access events. **Read access events** capture download events, while **write access events** capture write events. Each service can have different log categories for resources. The log categories for one service have no relationship to the log categories of another service.
    - Load Balancer : **Access Logs** and **Error Logs**
    - VCN Flow Logs : ALL (Accept and Reject records)
- Connector Hub
- Agent Configuration
- Service Log Format
    - standard CloudEvents v1.0 outer envelope format : specversion, id, type, and source
    - Oracle : logid, loggroupid, tenantid, compartmentid, ingestedtime
    - Data : includes specific details related to the log event, such as request parameters, response data, error messages, or any other relevant information that needs to be logged. 

- Custom Log ingestion from Custom Applications, On-Premises, , External to  OCI Environments

    - Unified Monitoring Agent : The Unified Monitoring Agent is a lightweight software agent provided by Oracle that can be installed on your on-premises infrastructure or virtual machines. It enables you to collect and send custom logs from your custom workloads running on-premises to the OCI Logging service. 
        - Unified Monitoring Agent is deployed in Source Environment Instances
        - OCI Logging Service Agent Configuration :
            - What hosts you want logs from : IAM Policy for Dynamic group to use log content and dynamic group rule (Grouping of hosts). 
            - What specific types of logs you want to collect : Windows event logs, Linux log directory (tail)
            - Additional parser configuration : JSON, CSV, Syslog, Auditd etc
            - Custom log destination : Defines compartment, Target log group and Log Object

    - API PutLogs : API PutLogs in OCI Logging service refers to the capability of using the OCI Logging API to manually send log data to the OCI Logging service. It allows you to programmatically upload log events or batches of log events to your log groups in OCI. 

- IAM Policies
    - Allow to manage log groups or objects
        - Allow group **Log-Admins-A**  to  *inspect log groups* in compartment ABC
        - Allow group **Log-Admins-B**  to  *read log groups* in compartment ABC
        - Allow group **Log-Admins-C**  to  *use log groups* in compartment ABC
        - Allow group **Log-Admins-D**  to  *manage log groups* in compartment ABC
    - Allow users to view logs
        - Allow group **Log-Viewers-A**  to  *read log-content* in compartment ABC
    - Allow to provision agent configuration
        - Allow group **Log-Admins-E**  to  *use unified-configuration* in compartment ABC
    - Allow instances to push logs
        - Allow dynamic group **production-fleet**  to  *use log-content* in compartment ABC

- OCI Audit Service : automatically records all calls made to supported OCI public API endpoints as log events
    - Retention : 365 dys at tenancy level
    - Record REST API calls
    - Includes header, time, source, target, type of action, request parameters, type of response, response parameters
    - Takes 15 minutes before an audit log is appended for an event
    - IAM Policies
        - Allow group **Auditors**  to  *read audit events* in tenancy
        - Allow group **Auditors**  to  *read audit-evnts* in compartment ABC

### Search and Analyze

- Search logs usingCustom, Basic and Complex Queries
- Log retention : 6 months (Service and Custom logs)
- Search using API SearchLogs and Command line interface (CLI) : ``` oci logging-search search-logs --search-query, --time-start, --time-end``` 
- Logging query specification
    - Log streams : Set of logs to be searched : ``` search <compartment_ocid> / <log_group_ocid> / <log_object_ocid> ```
    - Fields : ``` type, time, message, log_group, log_object, log_level, log_content, data.message, data.action ```
    - Data types : ``` string, integer, wildcard ```
    - Operators : ``` tabular, arithmetic, boolean, comparision ```
    - Expressions

### Take Action

- Write to Stream
- Invoke Oracle Functions
- Write to Object Storage

- Connector Hub : automatically records all calls made to supported OCI public API endpoints as log events
    - Source
    - Task
    - Target
        - Object Storage : Archiving
        - Oracle Functions : Automated remediation and alerting
        - Log Analytics : For deeper data analysis by ingesting data to Log Analytics
        - Streaming
        - Monitoring : Emit log metrics to OCI monitoring
        - Notifications : Move date to 3rd party tools and services



## OCI Events Service
OCI Events service enables you to define rules based on specific conditions and triggers, such as resource creation, deletion, or modification. When the specified events occur, the service can automatically invoke actions, such as invoking functions, sending notifications, or publishing messages to streaming services.

- Events : Structured and actionable messages that denote a change in a resource.
    - Examples:
        - User-initiated CRUD Operations
            - Create
            - Update
            - Delete
            - Read
        - Resource Lifecycle State changes
            - Started
            - Stopped
            - Deleted
        - System Events
    - Cloud Native Computing Foundation (CNCF) CloudEvents Format : Event JSON Object Structure
        - Standard event envelope is the outer container of the JSON message and holds metadata about the event itself. Key attributes are: eventId, eventType, cloudEventsVersion, eventTime, eventSource, compartmentId
        - The Service Specific innerpayload, contained within the data attribute of the envelope, holds the specific information about the resource and the change that triggered the event. Key attributes are: resourceName, resourceId, freeFormTags, UserdefinedTags, AdditionalDetails
    - Event Types : OCI event types are specific labels that identify changes or states within a particular OCI resource, such as a compute instance, a storage bucket, or a database service. Example : OCI  Compute has multiple event types : Autoscaling Event Types , Instance Event Types, etc.
        - Each Event Type is associated with multiple Events. Example : Instance Event Types has 22 events including launch instance begin, launch event end, terminate instance begin, terminate instance end etc

- Rules : Configuration where a user defines events that needs to be created , and triggers an action if they occur.
    - Which eventTypes to monitor
    - Which specific resources to filter  - Specify Compartment, Trigger Conditions using attributes or filter Tag
    - Which actions to be triggered


- Actions : User-defined responses to events that occur.
    - Oracle Functions:  serverless, fully managed compute service that lets developers build and run applications without managing the underlying infrastructure
        - You write your code and package it, along with its dependencies, into a container image. 
        - You push this image to a Docker registry, such as Oracle's Container Registry. 
        - You create and deploy a function in OCI, providing the link to the container image and other metadata like memory and execution time limits. 
        - The function is configured to be triggered by a specific event. 
        - When the event occurs, OCI Functions pulls the image, runs it in a container, executes the code, and returns the result. 

    - Oracle Notification Service: Send an alert or notification to a topic. 

    - Oracle Streaming Service: Send events to a stream for later processing or analysis. Create a stream 
        - Producers: A producer is a client application that writes messages to a stream.
        - Partitioning logic: Messages are routed to a specific partition based on a key.
            - With a key: Messages with the same key are always delivered to the same partition, guaranteeing ordered delivery within that partition.
            - Without a key: If no key is specified, OCI Streaming assigns one automatically to ensure that messages are distributed evenly across all partitions.
            - Performance: Each partition provides a specific throughput capacity. To increase the total throughput of a stream, you must increase the number of partitions. The number of partitions is set when the stream is created and cannot be changed later. 
        - Consumer groups: A consumer group is a set of consumer instances that work together to consume messages from a stream. The group coordinates message consumption so that each partition is assigned to only one consumer within the group.
        Publish-subscribe model: A publish-subscribe pattern is enabled by using multiple consumer groups. Different groups can consume the same set of messages independently, allowing multiple applications to process the same data stream for different purposes. 
        - Support Apache Kafka clients

## Log Analytics 

OCI Logging Service collects, stores, and searches logs from your OCI resources, acting as a centralized management and search hub for basic log access and retention. OCI Logging Analytics allows users to collect, monitor, aggregate, index, and analyze logs from various sources, including on-premises, multi-cloud, and OCI environments, to troubleshoot problems and gain operational insights.

Source type refers to the type of log source or service that determines the compatibility and supported collection methods in Logging Analytics. Required collection frequency, which is the frequency at which logs need to be collected and ingested into Logging Analytics, is an important consideration. Various business factors come into play when choosing a log ingestion method. Compliance regulations, security considerations, scalability requirements, and cost-effectiveness are among the factors that need to be assessed.

- Entity (System emitting logs)
    - Entity is representation of a system or a resource (Compute Instance, VNIC, VCN Subnet, Object Storage Bucket etc..)
    - Entity contains information about collection methods, system metadata, and the relationship with other entities
    - Log Sources are linked to one or more Entity Types
    - Entity can be a real system or a collection of systems that you want to monitor - Auto creation, discover using Stack Monitoring
    - Entities are used to set the analysis scope in Dashboards and Log Explorer

- Log Source and Collection Methods


    - Features of Log Sources 
        - is a configuration that tells Logging Analytics where to find logs and how to process them for analysis
        - Collection info, parser, data filters, extended fields, enrichments, labels
        - Parsers to extract values: RegEx, CSV, JSON, and XML. Parsers in Log Analytics are responsible for parsing or extracting structured data from raw log messages. They analyze the log messages based on defined parsing rules and extract relevant fields or attributes from the logs. This process allows for the transformation of unstructured log data into structured data, which can then be used for analysis, searching, and creating visualizations.
        - Labels are used to add problem context and severity to log
        - Lookup is a list of key-value pairs to enrich logs
        - Extended Field definitions and enrichments
        - Data Filters to mask, drop entry or string
        - Linked to one or more entity types that generate these logs
        - Entity Association: Each source is associated with one or more "entities" (the assets generating the logs) to correlate the data with the actual resources in your environment. Log Source-Entity association enables continuous log collection using Management Agent
        - Can be extended, duplicated, or created from scratch

    - Source Types
        - File Based
        - Database Table
        - Syslog Listenser
        - Windows event System
        - REST Endpoint (pull)

    - Continuos Collection Methods
        - Management Agent Cloud Service : Collects logs from on-premises or remote hosts where the agent is installed.
            - A cloud service that provides interactive communication and data collection between OCI and other Targets
            - Allows on-demand discovery, query and/or execution of select management operations on resources
            - Provides single egress, buffering, and aggregation of O&M data from a customer's network to OCI
            - Provides a self-monitored, fully managed solution with alarm capabilities for the agent
            - Configure Log Collection from Compute Instances
                1. Create Agent Install Key, Dynamic Group, and Required Policies : Use **Setup Ingestion** from the Logging Analytics Administration page.
                2. Install/Enable Management Agent: Install using the Agent Install Key from the previous step. Verify health on the Agent page.
                3. Enable Logging Analytics, map Entity to Agent: Host and Agent Entities are auto-created in Logging Analytics.
                4. Start Log Collection: Use the "Add-Data" wizard.
        - Service Collectors : Continuos Automated log collection from Monitoring OCI Services, Analyzing OCI Audit Logs, Analyzing Other Custom Logs. Its a feature within OCI Logging itself that automatically collects logs from OCI services, such as functions or object storage, and sends them to a designated location, like an OCI Logging log group or the Service Connector Hub
            - Continuous log collection - OCI Logging to Logging Analytics
            - Connectors created through Logging Analytics
            - Entities auto-created with Log Source associations
            - Customizable OOTB log sources for OCI services
            - Logs enriched with contextual information
        - Object Storage Log Ingestion (without installing agent) for Managing apps/services that push logs to Object Storage, Analyzing historical logs stored in Object Store and Ingesting large volumes of logs (multi-terabytes)
            - Object Collection Rule
                - New objects are automatically collected
                - OCI Events & Streaming used to monitor buckets
                - Created using OCI SDK, CLI, or Terraform
                - Can collect LIVE, HISTORIC, or HISTORIC_LIVE
                - Object Collection Rule Creation: OCI CLI Example
                - ```oci log-analytics object-collection-rule create --from-json file://create.json namespace-name MyNamespace```
                    ```text
                    {
                    "name": "<Rule_Name>",
                    "compartmentId": "<Compartment_OCID>",
                    "osNamespace": "<Namespace>",
                    "osBucketName": "<Bucket_Name>",
                    "logGroupId": "<Log_Group_OCID>",
                    "logSourceName": "<Log_Source>"
                    }
                    ```
        - FluentD
        - OCI Functions
        - EM-OCI Bridge
    
    - Manual Collection Methods
        - On-Demand Upload
        - REST API

    - Fields, Labels and Lookups - Overview
        - Fields data types: String, Long, Timestamp, Float, and Integer
        - Labels are used to annotate logs during ingestion
        - Labels can be informational or denote a problem with a severity level
        - Lookup is a list of key-value pairs used for enriching logs. Its a table that maps specific field values from your log data to more descriptive information.  Types are
            - Simple
            - Dictionary
    
    - IAM Policies. 

        - Logging-Analytics-Users
            - allow group Logging-Analytics-Users to read loganalytics-features-family in tenancy
            - allow group Logging-Analytics-Users to read loganalytics-resources-family in compartment myCompartment
            - allow group Logging-Analytics-Users to use management-dashboard-family in tenancy

        - Logging-Analytics-Admins
            - allow group Logging-Analytics-Admins to use loganalytics-features-family in tenancy
            - allow group Logging-Analytics-Admins to use loganalytics-resources-family in compartment myCompartment
            - allow group Logging-Analytics-Admins to manage management-dashboard-family in tenancy

        - Logging-Analytics Super Admins
            - allow group Logging-Analytics-SuperAdmins to MANAGE loganalytics-features-family in tenancy
            - allow group Logging-Analytics-SuperAdmins to MANAGE loganalytics-resources-family in compartment myCompartment
            - allow group Logging-Analytics-SuperAdmins to MANAGE management-dashboard-family in tenancy

- Data Governance and Access Control

    - Active Storage
        - Data always ingested in Active Storage first
        - Data available for exploration, analysis, dashboards, alerts
        - Data can be retained for any length of time as needed
        - Active Storage size can be managed by:
            - Archiving older data (if active storage > 1TB), OR
            - Purging data
    - Archival Storage
        - Automated to move data from Active to Archive storage (if Archiving enabled)
        - Data not available for exploration, analysis, dashboards, alerts
        - Data can be retained for any length of time as needed
        - Archived data can be temporarily recalled into Active Storage
        - Archival Storage size can be managed by purging older data from archive (policy-based)

    - Log Groups and Log Partitioning
        - A Log Group is a logical collection of log records
        - Log groups are logical containers for organizing logs. Logs must always be inside log groups. You must create a log group to enable a log. Use log groups to limit access to sensitive logs with IAM policy.
        - Log Groups are used to control access at the compartment level
        - Log Partitioning to partition logs based on Log Sets to improve log search performance
        - A default Log Set is created automatically until you configure Log Sets


- Insights (Explorer, Dashboard and Query Language)
    - Log Explorer
        - Graphical Interface for interactive visual analytics
        - Powered by sophisticated Query language
        - Provides easy-to-use Machine Learning techniques (Cluster, Link)
        - Allows you to create widgets and save directly to Dashboards for monitoring
        - UI Components
            1. Scope Filter: Limit analysis to Compartment, Region and/or entities
            2. Time Range and Actions Menu: Options to open, save, and export query with results
            3. Query Bar: Formulate search query and run
            4. Field Panel: Values emitted (parsed) from logs to filter, group, and compare
            5. Visualization Panel:Rich set of charts, field selections, and chart parameters

    - Dashboards: Provide information at a glance and quick access to specific metrics
        - Out-of-the-box dashboards
            - Enterprise Network (VCNs) Overview
            - OCI API Gateway Overview
            - OCI Audit Analysis
            - VCN Flow Logs
        - Custom Dashboards

    
    - Data Visualizations
        - Map Visualization to group log records by location
        - Word Cloud to view log records grouped by selected Fields
        - Line Chart to group by numeric Field and view Trends over time
        - Bar chart to view log records grouped by Entities or Log Fields
        - Histogram reduce log by grouping and drill-down to specific log
        - Sunburst used to analyze log data using multiple key parameters
        - Treemap to analyze data from multiple Fields that are hierarchical and fractional

    - Parts of Query Strings
        1. Search Strings
        2. Commands
        3. Functions
        4. Arguments
        5. Function Clauses
    
    - Log Clustering
        - Turn billions of log records into a few hundred patterns.
        - Group logs that have a similar structure.
        - Simplify the identification of issues from a large volume of logs.
        - Tabs
            1. Clusters
            2. Potential Issues
            3. Outliers
            4. Trends
            5. Cluster Sample
            6. Cluster Compare

    - Correlation with link command
        - Visualization with advanced Analytics
        - Combines individual log records across different Log Sources into groups
        - Advanced correlation techniques and identify patterns
        - Cross-correlation, Advanced filtering, classifications and Anomaly detection
        - Steps
            - Prepare, & clean data, Custom Time
            - Use link command to correlate a set of fields
            - Use classify command identify anomalies (categorical)
            - Use classify command to further (time based)
            - Pin-point & analyze specific pattens using filter
            - Example : 
            ```text
                'Log Source' = 'OCI Audit Logs" and "User Name' like in (%.%@oracle.com)
                | eval UserHashed = md5('User Name')
                | link Method,
                UserHashed
                Event,
                'OCI Resource Name"
                | classify topcount = 5 correlate = -
                'OCI Resource Name UserHashed,
                Count as "User Anomalies
                | classify 'Start Time',
                OCI Resource Name",
                Event, Method 'Audit Analysis
            ```

- Kubernetes Monitoring Solution
    - Platform: Kubernetes Control Plane and Components
        - Visibility into control plane health and performance for improving latency
        - Allows analysis of network traffic and configuration and flow logs for optimization
        - Performance and availability trends for API Servers and databases
    - Compute: Kubernetes Nodes Monitoring
        - Operating system issues and predefined problem signatures for alerting
        - Trends of resource usage and consumption for allocation
        - Correlate OS configuration (e.g. SE Linux setup) affecting apps
    - Multicloud Kubernetes Environments
        - Consistent view across clouds and on-premises environment
        - Customizable deployment options for GCP, Azure
        - Common parsers that allow comparison and contrast to evaluate optimal ROI
    
## APM : Application Performance Monitoring

### Key Purposes of APM

APM enables developers, DevOps engineers, and application administrators to:
- Monitor cloud native and enterprise applications. As applications become more complex and distributed, it becomes increasingly difficult to understand and manage the flow of requests through the system. In traditional single-process architectures, tracing requests is relatively easy, because all the requests are handled within the same process. However, in modern Cloud environments, applications are often composed of numerous microservices that run across multiple hosts and containers, making it much harder to trace requests and identify the root cause of issues.
- Visualize data with out-of-the-box and customizable dashboards
- Diagnose issues, identify the causes, and reduce down time
- Application Performance Monitoring (APM) uses the OCI Monitoring service to collect metrics (store data) and logs from applications running on OCI resources. These metrics can include resource utilization, response times, and error rates, among others. APM then uses this data to identify performance bottlenecks (query data, define alarms) and other issues, and to provide insights into how application performance can be improved.

By four distinct capabilities:
- Distributed tracing - Captures full traces for analysis and fine-grained problem diagnostics
- Real User Monitoring - Measures end-user performance from browser to server
- Synthetic monitoring - Runs predefined monitors from global locations for proactive monitoring
- Server monitoring - Watches performance and availability status from a rich set of servers

Which result in:
- Enhanced visibility into the performance of applications and infrastructures
- Improved end-user experience, and increased customer satisfaction

### Key Concepts and Terminologies
- APM Domain - Systems being monitored by Application Performance Monitoring
- Application Performance Index (APDEX) - An Open Standard to measure User satisfaction based on response time of applications. APDEX is often used as a key performance indicator (KPI) for measuring the effectiveness of application or service monitoring and optimization efforts. APDEX is used to monitor and evaluate the performance of applications and services. It provides a standardized metric to measure user satisfaction with response times and helps identify areas for performance optimization.
- Data Sources - Gather monitoring data from target applications, and upload to APM
- Traces - End-to-End flow of a request passing through all components of a distributed system. A trace represents a collection of spans that form a single, end-to-end trace through a system. Each span represents a single operation in the system. Spans are nested with child spans representing operations that are called by the parent span.
- Spans - Logical units of work with a name, start time, and duration within a trace. Each Span has:
    - An Operation name
    - Start and Finish time
    - Span context, data required to move across services - for example, Span id, Trace id, user-defined properties
    - Tags, user-defined annotations for querying data and filtering the request, and Logs, which capture log messages
- Distributed Tracing - Monitors transaction performance, volume, and error rate


- Real User Monitoring - Captures and analyzes user interaction with websites or application
- Synthetic Monitoring - Monitors and detects potential availability and performance issues before end user experiences it
- Vantage Points - Define locations to run synthetic monitors
- APM Browser Agents - Record user interaction with websites and send spans, metrics to APM : Add JavaScript manually to the application
- APM Java Agents - Record spans and metrics from application servers and send them to APM.  The role of the APM Agent in Oracle Cloud Infrastructure Application Performance Monitoring is to collect performance data from the application or service being monitored. The agent is deployed within the application environment and gathers metrics such as response times, resource utilization, and error rates. It then sends this data to the monitoring service, enabling real-time analysis and performance monitoring. Download agent from the APM domain, add to the monitored application using the "-javaagent" parameter
- APM Java Tracers - Record Open Tracing spans with application metrics and send spans and metrics to APM. Requires configuration and/or code change within the monitored application
- Open-Source Tracers - Other Open Tracing-enabled applications - Requires configuration and/or code change within the monitored application


### Perform Prerequisites: Required configurations in Oracle Cloud before using APM service
- Create or designate a compartment to use
- Create users and groups
- Create policies
    - Allow group APM-Grp to **read apm-domains** in compartment "myCompartment."
    - Allows users in the group "APM-Grp" to view the details of the APM domains created in the compartment "myCompartment."
    - Need a policy **manage apm-domains** permission in the target compartment
    - APM Domain can be of two types - Always Free (one per region, use 1000 spans per hour) or Paid (up to 3 per region)
        - Required policies :-
            - APM Policies : View details, list work requests, perform management tasks in APM domains
            - Monitoring Policies : Access APM metrics in Metrics Explorer, create alarms, and receive notifications
        - Optional policies:- 
            - Management Dashboard and Management Saved Search Policies: Create and edit custom dashboards and saved queries
            - Dedicated Vantage Point Policies for Non-Admin Users Optional: Set up a Dedicated Vantage point for non-admin users

    - Obtain data upload endpoint and data keys.
        - Copy the data upload endpoint and auto-generated data keys from the APM domain page.
        - Use when configuring data sources such as the APM Tracer or Java agent (public key) and the Browser agent (private key)
        - You can create additional public or private data keys
    
    - Span Filters and Metric Groups
        - Define a set of spans and metrics that are pushed to the monitoring service for the alerting and charting purposes


### Open Telemetry Operator for Kubernetes : Automatically inject APM Java Agent in Kubernetes
The existing ways to deploy Java agent in Kubernetes are:
    - Pre-deploy Java agent in the pods (needs dev involvement)
    - Set up Java agent in a shared disk (needs additional configuration)
A new way to deploy the Java agent in Kubernetes is to:
    - Utilize the Open Telemetry operator for Kubernetes

### What is Distributed Tracing?
- Is the solution for Tracing and Observing requests in Distributed Software Architecture
- Tracks workflows of a single request and collects data from services
- Helps pinpoint where failures and bottlenecks are occurring
- Tells how and where a single request is flowed through the Distributed System
- Standards
    - Open Tracing
        - A set of API that helps developers instrument tracing into their code
    - OpenCensus
        - A set of libraries to collect distributed traces
    - Open Telemetry
        - Merged the code bases of Open Tracing and OpenCensus
        - Offers single set of APIs and libraries for collecting distributed traces and telemetry data such as metrics and logs
- Trace Explorer Query
    - Use the SQL-like query language tailored to request distributed tracing data.
    - Quick-pick tabs enable you to predefine commonly used queries.
    - Trace explorer queries consist of clauses and dimensions.
    - Use dimensions to filter the selection.
    - Save and reuse the query.
- Geomap view
    - It is the global map view of clustered trace data.
    - Dark colors indicate higher value - for example, dark blue indicates a slower connection
    - Select metrics from Service, Operation, Duration (ms), and Span Errors.
    - Click countries to narrow down to states and provinces.
- Trace details view : Drill down to the individual spans
    - Trace details - Summary of the trace, for example, trace status, duration, trace id, number of spans, and so on
    - Topology - Relations among the operations (spans) and services
    - Spans - Hierarchical bar chart showing individual spans, duration. and dependencies
    - Span details -Selected span information, for example, operation name, span id, duration, and so on
- Enable Tracing with Oracle Functions : Steps to enable tracing of a function:
    - Create a logical group of functions, for example, "application."
    - Create a function within the application.
    - Specify the APM domain in the application where the spans will be sent.
    - Enable tracing in the function.
    - Default invocation spans are automatically created and sent to the APM domain.
    - Additional custom spans can be created manually.

### Obtain Permissions to Use Dashboard and Metrics: Required configurations in Oracle Cloud before using APM service
Assign Management Dashboard permissions
- Administrators can assign different levels of permissions to a user/user group. (inspect, read, use, or manage)
    - Example: Allow **group DASH-ADMINS** to manage management-dashboard in compartment ABC
Assign Monitoring Service permissions to a user/user group to:
- Access APM metrics in Metrics Explorer
- Create alarms and be notified
- View alarms on the APM home page
    - Example: Allow **group APM-USERS** to read metrics in compartment ABC
    - The above verb grants permission to read metrics in the compartment ABC to users in the APM-USERS group.

### APM metric namespace: The source service emitting the metric.
- oracle_apm_rum: Metrics related to Real User Monitoring (RUM), for example, Page activities, AJAX calls
- oracle_apm_synthetics: Metrics related to Synthetic Monitoring, for example, # of connections # of DNS lookups
- oracle_apm_monitoring: All other APM metrics, such as the metrics stemming from application servers and custom metrics

### Apdex thresholds
- Apdex is an open standard for measuring user satisfaction with the performance of applications.
- Apdex threshold rules are created in the APM Domain, used as performance in queries and dashboards metric, or as a dimension to filter traces in queries in Trace Explorer.
- Apdex measures user satisfaction with application response time.
- Good operations receive 1 point each, OK receive ½ point, and the Bad receive 0 points.

### Enable Metric Groups
Step1: Create a Span Filter
- Set of spans examined to compute the metrics in Metric Groups.
- Consists of two attributes: Name and Pattern: Example:
    - Span filter name: checkout-svc
    - Pattern: serviceName = 'wsk-store-front' and operationName ='/frontStore/checkout'
- Preserves query conditions
- Best practice: Copy query results from Trace Explorer.

Step 2: Create a Metric Group
- Custom dimension for a span metric, based on a span filter
- Created based on a span metric and a span filter: Example:
    - Metric group name: checkout-mg
    - Span filter name: checkout-svc
    - Namespace: oracle_apm_monitoring
    - Namespace metrics: SpanDuration, (additional metrics can be selected, for example, SpanErrorCount, ApdexScore, and so on)
- Metric data is pushed to the monitoring service for alerting and charting purposes.

### APM Browser agent and RUM
- Core functionality in Real User Monitoring (RUM)
- Generates and sends spans recording user actions
- Manually insert JavaScript or Java agent to automatically inject into the application
- Supports Single Page Applications (SPA), such as Angular or React web pages
- Create user-defined metrics for deeper data examination

### Synthetic monitoring
- Capability to run scheduled Monitors
    - Scripted Browser Monitor (Selenium test scripts)
    - Browser Monitor
    - REST Monitor
    - Scripted REST Monitor (Postman scripts)
- Collect HAR (HTTP Archive) file & Screenshots
    - Page, component, and ajax load details
    - Network trace details
- Oracle hosted and Dedicated Vantage Points Monitors
    - Available from every OCI datacenter
    - Dedicated Vantage Points
- Drilldown to the Trace Exploration page
    - See the backend execution details for each of the scripted steps
- Synthetic execution metrics
    - Available for dashboarding and alerting
Dedicated Vantage Points – Why do they matter?
- Applications on-premise or on secured network cannot be tested from a public Vantage Point.
- Test internal customer applications.
- Test if secured APIs can be reached for integration purposes
- Types
    - Application is publicly accessible from the OCI data centers : Public synthetic vantage points
    - Application is accessible within your own OCI network : Dedicated vantage points. Deploy it on your own OCI tenancy and have complete control of the VCN settings and monitor the private endpoints running on OCI
    - Application is accessible within your on-prem data center or in other clouds : On-Premises Vantage Point (OPVP) for Synthetic Monitoring : Set up OPVP at any location either on the premises or other cloud vendors to run your synthetic monitors securely from within your secured networks.
        - Follow the 3-Step Get Started Guide on the OPVP page to set up either on your stand-alone docker or in a Kubernetes cluster.
            - Dynamic groups and policies : Dynamic group and policy must be created to use on premise vantage point. User should have permissions to perform seamless actions in on-premise vantage point.
            - Download worker: There can be many workers at one on-premise vantage point. One worker can run one monitor.
            - Worker Usage Instruction : User can generate, copy and download configuration script to worker on docker or kubemetes after on premise vantage point is created and worker is downloaded.

## Stack Monitoring

Stack Monitoring: Essential Monitoring and Management for Applications and Infrastructure in Hybrid Environments
    - Monitoring Oracle applications and their components
    - Monitoring infrastructure/application stack technologies
    - Preconfigured UI for holistic monitoring across the stack
    - ML-based anomaly detection
    - Extensible to monitor anything
    - Manage alarms for outages or performance issues across systems or the fleet

Resource Home page provide quick access to key:
    - Load
    - Performance
    - Utilization metrics
Topology
    - Understand what is running on a host.
    - Correlate performance of database, WebLogic, etc. to the underlying host.
    - Easily navigate across the app stack.

Discovery Best Practices
- The first step is to discover all hosts (tip : enable host auto-promotion.)
- The second step is to discover the database, including any Pluggable DBs.
- The third step is to discover applications (EBS/PSFT) or application servers(WebLogic/Tomcat).
- The final stage, "Extensible," involves discovering resources like Prometheus, Telegraf, collectd, process-based resources, and importing OCI Services.

Extensibility: Monitor Almost any Resource and Enrich Application Topology with Custom Resources

Monitoring Any Host Process as a Resource : monitoring a custom application that is running in my environment
Process-based custom resources:
    1. Identify the processes that make up your app (name, etc.).
    2. Identify the hosts that it is running on.
Start monitoring:
- Stack Monitoring will create a resource instance and will monitor it for Status and CPU/Memory usage with baselines/anomaly detection enabled
- Create resource instances from Prometheus, Telegraf, and collectd metric sources.
- Can be associated with other resources and shown in topology views for performance correlation across the stack
- Built-in homepage unifying status, performance, and alarms

Identifying App-Specific Metrics Using Metric Extensions : Extend the out-of-the-box metric set to monitor conditions specific to your environment.
- Meet custom monitoring requirements.
- Easy to create with a simple fill-in-the-blank UI
- Leverages collection method supported for that resource type
- Uses the credentials the agent is using for monitoring the resource
- Metrics stored in OCI Monitoring
- Create alarm rules.
- Steps : Metric Extensions Life Cycle
    - Development (Draft) : Develop script, Create Metric Extension, Save Metric Extension
    - Testing (Draft) : Check metric results
    - Published : Enabled on resources, Metric collection starts
