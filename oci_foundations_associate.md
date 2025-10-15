# Oracle Cloud Infrastructure Fundamentals

## Introduction

- Regions (Geographic Area) have one or more Availability Domains (ADs) --> Fault Domain (Hardware + Infra) : Logical datacenters

- Region Selection
    - Location (Low Latency --> High Performance)
    - Data Residency and Compliance
    - Service Availability
    - Contain one or more ADs

- ADs --> Don’t share physical power / network. --> Hence Fault Tolerant
    - Each AD have 3 Fault Domains (Like logical data centers)
    - FDs will not  share single points of hardware failure
    - During instance launch time, we can select the FD in which instance to be launched
    - Oracle Data Guard : Sync DB between Ads

- Region Pair : Disaster Recovery    

- Oracle Roving Edge Infrastructure is a service designed to host workloads and use cases that operate at the edge, especially the far edge and tactical edge. It is designed to function as an extension of your Oracle Cloud tenancy.

- OCI Dedicated Region enables customers to deploy an Oracle Cloud region in their own data center.
        
## IAM and Access Management

- Identity based Access Control or RBAC or Fine grained Access Control.
- Key aspects : Authentication(AuthN Roles) : Who are you , Authorization(AuthZ Set of permissions) : What are you allowed to do

OCI Identity Concepts:
- Identity Domains : A User population in OCI and associated configurations and security settings.
    - Identity Domain --> User (Username and Password) --> Groups --> Roles --> Policies (RBAC) --> Compartments containing the resources 
    - Every resource will be having OCID (Oracle Cloud ID) : Syntax is  ```ocid1.<resourcetype>.<realm>.[region] [.future use].<unique id> ```

- Compartments : 
    - Helps to keep tenancy more organised. 
    - Tenancy/Root compartment having multiple sub-compartments like network compartment, storage compartment, database compartment etc.
    - Compartments helps to control access to resources in a compartment. Example : Admin Role in Admin Compartment can access all resources in all compartments.
    - Resouces an be moved from one compartment to other.
    - Compartments are a global resource. Resources from multiple regions can be in same compartment.
    - Compartments can be nested to 6 levels.
    - Quotas and Budgets can be applied to compartments.

- Authentication : Who are you ?
    - Principals : IAM entities that are allowed to interact with OCI resources using Username and Password, API Signing Keys, API Signing Certificates, Authentication Tokens, etc
        - IAM Users
        - Groups : Collection of IAM Users
        - Resource Principals
        - Dynamic Groups allow OCI Compute instances and other cloud resources to act as principals and receive permissions via IAM policies. This means that Compute instances, Functions, and other resources can have permissions without requiring a specific user to authenticate.

- Components: 
    - Location (compartment or tenancy).
    - Action Verb (the specific action to be allowed).
    - Resource (the resources the action can be performed on).
    - Principal (group the policy applies to).
    - And a set of optional Conditions.

- Authorization : What are you allowed to do ?
    - Policies that can be applied to compartment or tenancy.
        - Allow <domain_name>/<group_name> to <verb> <resource_type> in <location> where <condition>
    - Verbs : 
        - inspect (list resources) : 3rd party auditors
        - read(inspect + user-specified metadata) : Internal auditors
        - use(read + actions) : resource end users
        - manage  (all permisions for resource) : Admins
    - Resource Types :
        - All resources
        - Database Family
        - Instance Family
        - Compute Family
        - Network Family
        - Storage Family 
- Tenancy Setup :
    - Tenancy Admin (Not for day to day operations) --> OCI admin users --> OCI Admin group --> Policies --> Compartments like Prod/Dev/Test etc
    - Enforce MFA 
- Demo : 
    - Create Identity Domain and a user
    - Create group
    - Associate  Identity Domain's group to user
    - Create Policy

## Networking

- VCN : Virtual Cloud Network : Highly Available, Scalable and Secure : CIDR Block 10.0.0.0/16
    - Local Peering using Local Peering Gateway for communication between 2 VCN in same Data Centre Region
    - Remote Peering using Dynamic Routing Gateway (DRG) and Oracle Backbone for communication between 2 VCN in different Data Centre Region
    - Dynamic Routing Gateway v2 (DRG v2) : For communication between many VCNs inside same Data Centre Region
    - Default Security List, Route Table and DHCP Options are created

- Subnets
    - Public  : CIDR Block 10.0.0.0/24
    - Private  : CIDR Block 10.0.1.0/24

- Internet Gateway : a virtual router that provides a path for a virtual cloud network (VCN) to connect to the public internet, allowing both incoming (ingress) and outgoing (egress) traffic

- NAT Gateway : NAT as a service : Unidirectional: Enable outbound traffic to internet blocking inbound traffic. allows resources in a private subnet, such as compute instances without public IPs, to initiate outbound connections to the internet while preventing inbound connections from the internet

- Service Gateway : provides private, secure access to multiple Oracle Cloud Infrastructure (OCI) services from within a Virtual Cloud Network (VCN) or on-premises network without using the internet. This gateway routes traffic to supported Oracle services over the OCI network fabric instead of public IP addresses, keeping data protected. enables access to Oracle services within the same region without the traffic going through the public internet. This provides a more secure and reliable connection for accessing Oracle services like Object Storage, Autonomous Database, and others.

- Dynamic Routing Gateway (DRG) using site-to-site VPN / fast Connect and CPE :Customer Premises Equipment. Between VCN and On-prem Network. a virtual router that connects a Virtual Cloud Network (VCN) to other networks, such as on-premises networks, other VCNs, and the internet. It acts as a central point for routing traffic, supporting both static and dynamic routing with features like BGP support.  provides a path for traffic between a VCN and an on-premises network or another VCN in the same or different region.

- Route Table determines where traffic goes by defining rules for traffic exiting a subnet, while a security list acts as a virtual firewall that controls what traffic can enter or exit a subnet. It has Destination CIDR Block(192.168.0.0/16) and Route Target (Eg.Dynamic Routing Gateway) . You create route rules to send traffic to specific targets, such as an Internet Gateway (for public traffic) or a NAT Gateway (for instances in a private subnet requiring outbound internet access)

- Firewall rules are set using Security Lists or Network Security Groups, not Route Tables.

- Security List : A set of stateful or stateless rules that act as a virtual firewall for a subnet within a Virtual Cloud Network (VCN). These rules control the ingress (inbound) and egress (outbound) traffic for all resources, or VNICs (Virtual Network Interface Card), within that subnet by allowing or denying traffic based on protocols, ports, and source/destination IP addresses. Each VCN has a default security list that is automatically associated with subnets unless a custom one is specified. A single subnet can be associated with multiple security lists
    - Stateful : the response to an outgoing connection is automatically allowed
    - Stateless : both the request and response must have a rule

- Network Security Group(NSG) : a virtual firewall that uses ingress and egress security rules to control network traffic for a set of resources, such as Compute instances, within a Virtual Cloud Network (VCN). Unlike a security list, which applies rules to all resources in a subnet, NSGs offer more granular control by applying rules to specific resources, even if they are in different subnets. Resources in different subnets can be placed in the same NSG if they share the same security posture. The key difference between Security Lists and Network Security Groups in Oracle Cloud Infrastructure is that Security Lists apply to subnets, while Network Security Groups apply to individual instance VNICs. This allows for more granular control of traffic in and out of instances.

- Load Balancers : Can be Public or Private. provides automated traffic distribution from one entry point to multiple servers reachable from your virtual cloud network. improves resource utilization, facilitates scaling, and helps ensure high availability.
    - Application layer : Layer 7 HTTP Load Balancer : More intelligent / less performance, and can handle application-specific traffic
    - Laayer 4 and Layer 3 Network Load Balancers : More Performance, and can handle any type of TCP or UDP traffic.
    - supports three types of load balancing algorithms: Round Robin, Least Connections, and IP Hash. The Round Robin algorithm distributes incoming traffic evenly across instances. This algorithm helps ensure efficient distribution of network traffic and maintain the availability and performance of applications.

- Demo 1 : Create VCN using VCN wizard
    - OCI Console --> Networking
    - Virtual Cloud Networks --> Start VCN Wizard --> Simple VCN
    - Network Visualizer

## Compute

- Compute Shapes and sizes
    - Standard Shapes offer a balance of cores, memory, and network resources for general purpose workloads
    - DenseIO Shapes for high-performance workloads that require large amounts of locally attached storage
    - GPU Shapes for hardware-accelerated workloads accelerated computing
    - HPC Shapes for high-performance computing with high-frequency processor cores and cluster networking
    - Flexible Shapes are a customizable option within the Standard category. users can customize the number of OCPUs and the amount of memory according to their needs. Flexible shapes provide more control over resource allocation and help users optimize costs and performance.

- Types
    - Bare Metal : Direct hardware Access to a dedicated physical server, No VMs  Single-Tenant Server
    - VM : Hypervisor to virtualize underlying bare metal server, Multi-Tenant VM
    - Dedicated VM Host: Bare metal server with Hypervisor that is entirely dedicated to a single customer, Single-Tenant VM

- Compute Processors : Intel, AMD, Ampere ARM

- Pay for what you use

- Preemptible VMs : behave the same as regular compute instances, but the capacity is reclaimed when it's needed elsewhere, and the instances are terminated. Short lived VMs ideal for  for workloads that can be interruptedfor Batch jobs. Designed for short-term usage

- Compute Control Plane has dependency on Network Control Plane and Block Storage control Plane

- Live Migrate between Hosts : a feature that moves a running virtual machine (VM) from one physical host to another with minimal or no downtime . You can Opt in or opt out . 
Live Migration is a feature in the Oracle Cloud Infrastructure Compute service that enables users to migrate running instances between different fault domains without any downtime. It allows users to perform maintenance or balance workloads across fault domains while maintaining the availability and performance of their applications.

- Demo
    - Initiate cloud shell from developer tools menu of OCI console
    - ```mkdir .ssh```
    - ```cd .ssh```
    - ```ssh-keygen -b 2048 -t rsa -f demokey ```
    - Private Key and Public Keys will be generated
    - Create VCN (IPV4 CIDR Block 10.0.0.0/16)
    - Create Public Subnet (CIDR Block 10.0.0.0/24) with Default Route Table and Security List
    - Create Internet Gateway 
    - Add Route Rules to VCN Default Route Table : Destination CIDR Block 0.0.0.0/0 and Route Target Internet Gateway
    - Add ingress rule to VCN Security List : Source CIDR Block 0.0.0.0/0 and Destination Port 80
    - Create Instance with Public IP : On demand capacity, Paste generated Public Key
    - in Cloud shell, ```ssh -i  demokey opc@ipv4```

- Scaling : 
    - Vertical Scaling : Increase of CPU and memory, Instance shapes are changed, Require downtime, Good practice is to stop instances before scaling.  process of increasing the performance of an instance by adding more OCPUs and memory. This allows for improved processing capabilities and resource allocation, resulting in enhanced performance for the compute instance.
    - Horizontal Scaling : High availability without downtime, Create Snap of existing instance --> Create Instance Pool to place new instances in different ADs --> Write Auto Scaling Rules (CPU and memory thresholds, Minimum and Maximum instances) . This type of scaling helps manage workloads and maintain optimal resource utilization by distributing traffic across multiple instances.

- Instance Configuration : an Instance Configuration is a predefined configuration that includes the instance's shape, base image, and metadata. It allows users to quickly create new instances with the same configuration, streamlining the deployment process.

### OKE : Oracle Kubernetes Engine

- Docker : Container Runtime used to Manage and Build container

- Kubernetes : 
    - Auto scale (HPA automatically scales the number of Pod replicas in a Deployment and VPA automatically adjusts the CPU and memory requests and limits for containers within Pods based on their historical resource usage , Cluster Autoscaler (CA) automatically adjusts the number of nodes in a Kubernetes cluster ), Auto heal (Deployments replicas, Liveness and Readniss Probes), Simplified Deployment
    - Componenets of K8S OKE cluster :
    - Oracle Managed Kubernetes control plane:
        - Kube-controller-manager daemon embeds the core control loops shipped with Kubernetes. It runs various controllers that continuously monitor the state of the cluster and take corrective actions to ensure the actual state matches the desired state
        - Cloud-controller-manager embeds cloud-specific control logic and is responsible for interacting with the underlying cloud provider's API. It allows Kubernetes to integrate with various cloud services, such as creating and managing load balancers, volumes, and routes within the cloud infrastructure
        - Kube-scheduler responsible for watching for newly created Pods with no assigned node and selecting a node for them to run on
        - Kube-API Server exposes the Kubernetes API, which is used by various components and external users to interact with the cluster. All communication between the control plane components and between the control plane and nodes goes through the API server.
        - etcd : consistent and highly available key-value store used to store all cluster data, including configuration data, state information, and metadata about objects like Pods, Deployments, Services, etc. It serves as the single source of truth for the cluster's desired state.
    - Customer managed :
        - Nodepool
        - Node
            - Managed Nodes : OCI Compute instances within your tenancy that you partially manage along with Oracle, offering more control and customization for general-purpose workloads. in Both Managed and Enhanced clusters
            - Virtual Nodes : provide a serverless Kubernetes experience where you don't manage any underlying infrastructure, ideal for minimizing operational overhead, Auto K8s software upgrades,Only in enhanced clusters
        - Pod
        - Container
    - Types
        - Enhanced Clusters : include advanced features like virtual nodes, cluster add-on management, and workload identity, along with a financially backed Service Level Agreement (SLA)
        - Basic Clusters : provide core Kubernetes functionality with a Service Level Objective (SLO), Doesnt have financially backed Service Level Agreements (SLA)

- Serverless : compute service that allows you to run containers without managing any underlying infrastructure. It's a serverless option for containerized workloads that don't require a full orchestration platform like Kubernetes

- Serverless with Oracle Functions : compute service that allows developers to create, run, and scale applications without managing any underlying infrastructure. Event driven code execution
    - Code --> Push container to registry --> Configure function trigger  --> Code runs only when triggered --> Pay for code execution time only

## Storage

- Parameters to consider for choosing Storage
    - Persistant (Safe Storgae) vs Non Persistant
    - Data Types : Databases, Videos, Audio, Photos, text
    - Performance
    - Capacity
    - IOPS
    - Throughput
    - Durability (Replicatons of Data)
    - Connectivity : Local vs Network
    - Protocol : Block / File / HTTP

### Types

- Local NVMe : high-performance, locally attached NVMe SSD storage for specific OCI compute instances (like DenseIO shapes), ideal for demanding workloads like big data and databases. It offers very low latency and millions of IOPS but is not durable, meaning data is lost if the instance fails or is terminated, and users are responsible for protecting it through methods like software RAID

- Block Volume network-attached storage: versatile, durable option
    - Raw blocks of data; must be formatted with a file system (e.g., NTFS, ext4) by the OS
    - OCI Block Volumes are automatically replicated within an availability domain for high durability, ensuring data redundancy and protection against hardware failures.
    - Tiers
        - Lower Cost tier 2 IOPS/GB (0 VPUs) : Streaming, data warehousing
        - Balanced tier 60 IOPS/GB (10 VPUs) : Boot volumes, databases, SharePoint
        - Higher Performance tier 75 IOPS/GB (20 VPUs) : High-performance databases
        - Ultra High Performance tier 90-225 IOPS/GB(30+ VPUs) : Demanding workloads like high-performance databases
    - Performance based autotuning feature enables Block Volume to adjust the volume's performance between levels you specify, based on the actual monitored performance of a volume.
    - Detached volume performance autotuning feature enables Block Volume to adjust the volume's performance level to the optimal level based on the attached state of the volume. : Detached : Change volumne performance to lower cost, ReAttached : Volumne performace is auto adjusted
    - Multi VMs can share same block
    - Online(keeping attached) or offline resizing
    - Async Replication for Bloack Volumes
    - Volumne groups : For easy management, Time consistet Backups
    - Attachment Type : Parvirtualized
    - Higher latency than local NVMe and more expensive than object storage. 

- File Storage viz Shared file systems for applications that need data accessible by multiple compute instances
    - Supports NSv3 distributed systems
    -  File Storage service uses the Network File System (NFS) protocol for file access. NFS allows clients to access files over a network in a manner that appears as though they are part of the local file system.
    - Hierarchical, like a traditional file system (folders and files)
    - Use Cases
        - EBS
        - General Purpose File systems in Coorporates
        - Microservices and Containers

- Object Storage for multimedia viz accesible using PUT and GET from internet
    - Data managed for Objects in key-value form and metadata, Flat structure of objects with unique IDs and metadata; accessed via APIs
    - Stored in a bucket
    - Unique name in tenancy
    - For unstructured / semi-structured data
    - Multiple tiers
        - Standard (Hot) : Fast , frequent access, cant be downgraded
        - Infrequent Access (Cold) : cost less, minimum retention 31 days, retrieval fee , 60% cheaper than standard
        - Archive Storage (Cold) : minimum retention 90 days, need download before restore, restore time 1 hour, cant be upgraded
        - Auto Tiering
        - Lifecycle management : Rule like transition data to cold storage after 30 days
        - Versioning
        - Data Encryption
    - Object Storage API Endpoint
    - /n/Namespace/b/Bucket/o/Object

- OCI Migration Services
    - Data Transfer Disk : Send the disc to Oracle, and they move it to Oracle Storage
    - Data Transfer Appliance : A physical, secure appliance provided by Oracle for transferring large amounts of data

## Security

- Shared Security Model
    - the customer is responsible for securing their data, applications, and access control. This includes implementing appropriate security measures such as encryption, user access control, and monitoring to protect sensitive data and ensure the overall security of their cloud environment.
    - Oracle is responsible for Oracle Cloud Infrastructure services and functionality, such as Load Balancing, WAF, Cloud Guard, distributed denial-of-service (DDoS) protection, Compute, network, and storage isolation,IAM framework, Data center physical security ,Hardware, software, networking, and facilities that run Oracle services

- Infrastructure Protection
    - Web Application Firewall (WAF) : Protects web applications from common attacks such as cross-site scripting (XSS), SQL injection, and DDoS attacks. It can block traffic from specific countries or based on HTTP headers. WAF inspects incoming web traffic and filters out any requests that match predefined security rules, ensuring the security and availability of your web applications.
    - Netwrok Firewall : A fully managed, next-generation firewall service from a partnership with Palo Alto Networks to protect cloud workloads and integrate with native OCI services.
    - Security Lists / Network Security Group
    - DDoS Protection : automated DDoS detection and mitigation for high-volume volumetric attacks across its entire address space. This protection is always on and managed by Oracle.

- Identity and Access Management
    - IAM
    - MFA
    - Federation : the process of integrating Oracle Cloud Infrastructure's Identity and Access Management (IAM) with an external identity provider (IdP) like Active Directory or Azure AD. This allows users to access OCI resources using their existing credentials from the external system, enabling single sign-on (SSO) and eliminating the need for separate OCI accounts
    - Audit : captures system activity such as successful and failed sign ins, user creation, update and deletion, and so on

- OS and Workload Protection
    - Shielded instances : a security feature that hardens the firmware of bare metal and virtual machine (VM) instances to protect them against malicious boot-level software, such as bootkits and rootkits. They provide an additional layer of security by verifying the integrity of the boot process before the operating system loads. 
    - Dedicated Host : Dedicated Virtual Machine (VM) Host in OCI is a single-tenant physical server used to run VM instances that are not shared with other customers
    - Bastion Host : managed service that provides secure and time-limited access to private resources that do not have public endpoints. It acts as a secure intermediary or "jump box" for connecting to instances in a private subnet. 
    - OS Management : simplifies the management and monitoring of updates for operating systems running on compute instances. It helps maintain the security and health of your systems by applying critical updates and patches

- Data Protection
    - Vault Key Management : A managed service for securely storing and managing encryption keys and secrets, using envelope encryption with master keys and data keys to protect data
        - OCI vault services
            - Envelope 2- tier encryption : This is the two-tier encryption process. A master key encrypts a data key, and the encrypted data key is then used to encrypt the data. This is a common method for securing large amounts of data, as it uses the master key to protect the data key, which in turn encrypts the data.
                - Master keys: These are high-value keys that you create and manage in a vault. They are used to encrypt other keys.
                - Data keys: These are used to encrypt your actual data. When a data key is needed, a master key is used to encrypt it, creating an encrypted data key.
            - Rotate Master Keys : Periodically rotating master keys is a security best practice. Rotating a key creates a new key version, limiting the amount of data encrypted by any single version and reducing the risk of a compromised key. The old versions remain available for decryption purposes, but cannot be used for new encryption after rotation.
            - OCI Vault services uses HSMs that meet Federal Information Protection (FIP) requirements 140-2 Security level 3 security certification. Delete keys when detects tampers and require identy based authentication
    - Vault Secret Management : A feature of the Vault service that securely stores credentials and secrets. Integrates with services like OCI Load Balancing to provide secure, encrypted access to your web applications and servers. 
    - Data Safe : A unified control center for protecting sensitive and regulated data within Oracle databases. Provides features for assessing security risks, masking sensitive data, and monitoring user activity to help meet compliance requirements
    - Certificates : A service for creating and managing Transport Layer Security (TLS) and Secure Sockets Layer (SSL) certificates

- Detection and remediation
    - Cloud Guard : a cloud-native security service that helps Oracle Cloud Infrastructure (OCI) users maintain a strong security posture by monitoring for misconfigurations and risky activities. It identifies security problems using detector rules, which can then be addressed through responder rules and recipes that suggest, assist, or automatically take corrective actions. 
        - Targets(Resource Locations) --> Detectors(Policies to identify issues) --> Problems(Breach of policy triggering alerts) --> Responders(Corrective action)
    - Security Zones : a feature that automatically enforce security policies on OCI compartments (Networking (like subnets), Storage, compute and database resources) to prevent actions that could violate security posture . . You cannot disable security.
        - They work by creating a secure association between a compartment and a security zone recipe, which is a collection of security policies. Any attempt to create or modify resources in a way that violates the policies will be denied. 
    - Security Advisor services : Functionality encompassing security zones and cloud guard that can be applied to Object storage, block storage , file storage systems and virtual machines
    - Threat Intelligence : a free service in Oracle Cloud Infrastructure (OCI) that aggregates threat data from multiple sources to provide actionable guidance for threat detection and prevention
    - Vulnerability Scanning : Vulnerability Scanning
 
- Encryption
    - Encryption : Plain text + Key + algorithm = Cipher text
    - Decryption : Cipher text + Key + algorithm = Plain text
    - Encryption at rest : Ensures data is unreadable without the keys needed to decrypt
    - Encryption in transit (Eg. HTTPS) : secures data as it moves between services, regions, and users by using protocols like Transport Layer Security (TLS) 1.2+ for control plane data and MACsec for data between availability domains. This prevents data from being intercepted or tampered with while in motion
    - AES  (Advanced Encryption Standard) Symmetric key cryptography : Single Key is used for encryption and decryption
    - RSA (Rivest-Shamir-Adleman)Asymmetric key cryptography : Different keys (Public key used to encrypt and Private Keys used to decrypt) are used for encryption and decryption 
    - ECDSA : Elliptic curve cryptography used for digital signatures. known for providing a high degree of security with smaller key sizes compared to other algorithms like RSA, making it efficient and scalable. ECDSA is widely used in security systems, secure messaging apps, code signing, and is the basis for the security of many cryptocurrencies, including Bitcoin
    - Hardware security modules(HSM) : Physical computing device that safeguards and manages digital keys, performs cryptographic operations. They are tamper-evident
    
## Governanace and Administration

### Pricing : Simple, Lower and Transparent
- Pricing Models
    - Pay as you go (PAYG)
        - Consumption Based Pricing
        - No upfront commitment
        - No minimum service period
        - Usage metered
        - ideal for unpredictable workloads or for determining average usage
    - Annual Universal Credits
        - Must use annual credits within 12 months, After credit its PAYG
        - Discount based on size of deal and term of deal
        - Commit to annual pool of funds
        - You get a lower price than PAYG, and the credits are flexible and can be used across all OCI services in any region throughout the year
    - Monthly  Universal Credits
        - Similar to the annual model, but you commit to a monthly credit pool. You are billed in advance for this commitment, and any usage over the monthly limit is billed separately. 
    - Bring your own License (BYOL)
        - Customers running on premises licenses and want to reuse them in cloud. Customers to use their existing on-premises Oracle software licenses with OCI's pay-as-you-go or Universal Credits model, reducing costs by not paying for new licenses on the cloud

- Same price all over the world

- Factors that impact pricing
    - Resource Size : Larger or more powerful resources (more CPU, memory) cost more.
    - Resource Type : Using bare metal instances is more expensive than virtual machines (VMs) because it provides direct hardware access
    - Storage: The amount of data stored, the type of storage (e.g., block volume performance tier), and the associated performance cost (in GB/month and VPU/GB) all contribute to the price
    - Data Transfer
        - upto 1/4 of bill
        - Data egress: Costs are associated with data leaving OCI Region, which can be a major expense.
        - Data ingress: There is no cost for incoming data transfers.
        - Inside region is free transfer between ADs

- Cost Management Tools
    - Budgets : Set spending thresholds for compartments or tags and receive alerts when costs approach or exceed these limits. Budgets are "soft limits," meaning they don't stop resources from being created but trigger notifications so you can take action.
    - Cost Analysis : Visually track and optimize spending through interactive charts and tables. Allows you to filter and group your spending data by various parameters, such as compartment, service, tag, or SKU.
    - Cost and Usage Reports : Provide a detailed, resource-level breakdown of costs and usage for invoice reconciliation or audits. Cost reports include cost data at the resource-level granularity, while usage reports focus on the detailed breakdown of resources.
    - Scheduled Reports : Automate the delivery of Cost and Usage Reports to users on a recurring basis.
    - Service Limits, Quotas and usage in traditional tenancy, You can submit support requests for any quota increase
    - Compartment Quotas : Write policies to limit resources in a compartment : Set/Unset and zeroing(Remove access) down. Eg. Set max number of coud resources that can be used for a compartment

### Governance

- Cloud Advisor : provides actionable recommendations across cost, performance, security, and high availability to help you optimize your cloud environment
    - Recommendations
        - Cost Management : Identifies underutilized resources (like compute and databases) and suggests ways to downsize them to save money. It also provides estimated cost savings for applicable recommendations.
        - Performance : Analyzes resource utilization and recommends adjustments, such as rightsizing compute resources based on CPU usage thresholds to achieve a target average utilization.
        - Security : Leverages Cloud Guard to find and provide solutions for security vulnerabilities. It includes a security score, risk score, and snapshots of security problems, which can be viewed by compartment, region, or resource type.
        - High Availability: Offers best practices for managing potential hardware failures and improving the overall availability of your services. 

- Tagging
    - Organize resources
    - Cost Management
    - Tag based access control
    - Types
        - free form Tags : simple, user-defined key-value pairs with no predefined structure, allowing for flexibility but also potential inconsistency, No defined schema or access restrictions
        - defined tags (recommended) : More features and control, defining schema and access restrictions, contained in Namespaces, support policies
            - Tag Namespace is a container for a set of tag keys with tag key definitions
            - Tag key definition specifies its key and what types of values are allowed
            - tag key definitions or a tag namespace cannot be deleted, but retired
            - Example : Operations.Environment="Production" (namespace.Key="Value")
            - Keys are case-insensitive, and values can be a predefined string or a list of strings.

- Support rewards : a program that allows customers to earn rewards for spending on Oracle Cloud Infrastructure (OCI) that can then be used to offset the cost of their on-premises Oracle software license support bills
    - You earn rewards based on your consumption of OCI services. The reward rate is typically $0.25(Non ULA) to $0.33(ULA) for every $1 spent on OCI.
    - ULA : Unlimited License Agreement
    - Oracle support rewards based on Usage and type of Customer License  (ULA or Non- ULA)


        
