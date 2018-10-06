# AWS CSAA Study Notes

## VPC

### Security Group - _Settings_
+ In _**Default**_ Group
  - **DO NOT**  have explicit **DENY** rules.
  - All **outbound** traffic is **allowed** by default.
  - By default there is a **0.0.0.0/0** on All protocals/ports allowed **by default - OUTBOUND**.
  - Inbound rules allowing Instance assigned the **same** security group to talk to one another.

+ In _**Custom**_ Group
  + All **outbound** traffic is **allowed** by default.
  + All **inbound** traffic is **denied by default** in a Custom security group.

### Security Group - _Configurations_
+ Configuring a VPC using the VPC wizard that has a VPN-Only and VPN Access implies:
  + No public subnet
  + No NAT instance/gateway
  + It will create a Virtual Private Gateway (VGW) but **without an Elastic IP**
  + It will create a VPN connection

### Direct Connect (DX)
+ It is a direct connection (**not internet based**) and provides for _**_higher speeds (bandwidth)_**_ , _**less lantency**_ and _**higher performance**_ than Internet.
+ A Virtual Interface (VIF) is basically a 802.1Q VLAN mapped from the _**customer router**_ to the Direct Conncet router.
+ You need one **private** VIF to connect to your **private** VPC subnets, and one **public** VIF to connect your **AWS public services**.
+ You can NOT establish layer 2 over your DX connection.
+ You can NOT use a NAT instance/gateway in your VPC over the direct connection connection.
+ VPN will **NOT** provide bandwidth and latency guarantees.

### AWS Console - _Deleting a VPC_
+ When you launch a VPC with **public, VPN only subnets and a hardware VPN**, the following also gets created:
  + VGW
  + VPN connection is defined and created
+ You can delete this VPC, everything will be deleted except **VGW and VPN connection**.
  + VGW will be detached from the VPN
  + VPN Connection will still be using the VGW

### VPC Wizard Configurations
+ Target "**ALL**" is not a valid **Target** for **Route tables**.
+ This is the entry that is created by default in "any" route table to allow VPC subnets to communicate with one another.
+ The VPC will have an Internet Gateway (IGW) created and attached.
+ The **custom route** table will have an empty pointing at the VPC IGW as a target for 0.0.0.0/0 for Internet Access.
+ For VPC Wizard configuartions, when a public and private subnets are created, **two route tables** are created by AWS for you.
  - **Private** IPv4 subnet ---> **MAIN** VPC route table
  - **Public** IPv4 subnet ---> **CUSTOM** route table

### Launching EC2 instances in a VPC
When launching an EC2 instance in a VPC you can:
  + Leave it to AWS to select the best AZ for your EC2 instance
  + Or, specify which AZ you want your EC2 instance launched in
  + Also, you can leave it to AWS to assign an IP address for your instance
  + Or, you can assign the IP Address (if you choose the subnet for the EC2 instance) in the subnet where you want to launch your EC2 instance

### Designing VPC Subnets
+ To configure an ELB to load balance to instances in a subnet in a specific AZ, the **ELB must have one _public_ subnet** defined for the ELB in that AZ.
+ Web instances that are being load balanced in an AZ can be on **public** or **private** subnets.

### AWS Direct Connect Virtual Interface
+ You **must** create a **virtual interface** to begin using your AWS Direct Connect connection. 
+ You can create a **private virtual interface** to connect to your VPC, or you can create a **public virtual interface** to connect to AWS services that aren't in a VPC, such as Amazon S3 and Amazon Glacier.
+ You can configure **multiple virtual interfaces** on a single AWS Direct Connect connection.
+ For private virtual interfaces, you need one private virtual interface for each VPC to connect to from the AWS Direct Connect connection, or you can use a _Direct Connect gateway_.

### VPC Endpoint
+ Enables creation of a private connection between your VPC and another AWS service using its _private IP Address_. It currentl supports endpoints for **S3** and **Dynamo DB**.
+ Endpoints are supported within the **same region only**. You cannot create an endpoint between a VPC and a service in a different region.
+ VPC endpoint always **takes precedence over** NAT Gateway or Internet Gateway. In the absence of VPC endpoint, request to S3 are routed to NAT Gateway or Internet Gateway **based on** their existence in route table.
+ A VPC endpoint policy is an **IAM resource policy** that you attach to an endpoint when you create or modify the endpoint. 

### NAT Instance as a Proxy
+ NAT instance in **VPC** can be used with a public IP or Elastic IP address.
+ Does a PAT function, so it can hide more than one device/EC2 instance behind it when the traffic is initiated from these instances behind the NAT instance.
+ **NAT instance is a user responsibility and is not a managed AWS service (Unlike the NAT gateway).**
+ NAT instances can be deployed accross AZ's independetly for HA. EC2 instances, in each AZ, on private subnets requiring NAT should be configured to use the local NAT instance in the respective AZ.

### NAT Gateways
+ You can use a network address translation (NAT) gateway to enable instances in a **private subnet** to connect to the internet or other AWS services, but **prevent the internet** from initiating a connection with those instances.
+ NOT Support IPv6.
+ Internet Gateway are **two-way** traffic.
+ When creating NAT Gateway, there is an option to select **public subnet** in which NAT Gateway will be created. This **must be a public subnet** which has a route to internet through internet Gateway. If a private subnet is selected when creating NAT Gateway, it cannot route traffic to internet and hence the requests would fail.
+ NAT Gateway doesn't have **security groups**.

### Route table
+ Each subnet in your VPC **must be associated** with a route table.
  + A subnet can only be associated with **one** route table **at a time**, but you can associate **multiple** subnets with the **same route table**.
  + Any route table _local route_ **cannot** be **edited** or **deleted**.

### Bastion Host
+ Bastion hosts are instances that sit within your **public subnet** and are typically accessed using **SSH** or **RDP**.

### VPC Flow Logs
  + VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC.
  + Flow log data can be published to Amazon **CloudWatch** Logs and Amazon **S3**.
  + After you've created a flow log, you can retrieve and view its data in the chosen destination.

***
## EC2
### EC2 - _Enhanced Networking_
> + Enhanced networking is **not** supported on **all** EC2 instances
> + Enhanced networking does **NOT** cost extra
> + Enhanced networking can be enabled on **Instance-store backed** or **EBS-backed EC2 instances**.
> + Enhanced networking **requirements**:
>   + Instances be launched from an **HVM AMI** (no PV)
>   + Is ONLY supported **in a VPC**

### EC2 - _Placement Groups_
> + Is a feature that allows involved EC2 instances to communicate via **high bandwidth** and **low latency** connections **within an AZ**.
> + Placement groups **must** have all instances in the **same** AZ (only one single)
> + You can use **Enhanced networking instances** in **Placement groups**.
> + A placement group is ideal for EC2 instances that require **high network** throughout and **low latency** across **a single AZ**.

### EC2 - _Bastion Host_
> + Bastion host needs to be deployed in a **public** subnet
> + You can allow RDP or SSH or both based on the Windows or Linux instances you need to manage respectivly
> + Bostion host can have an Elastic IPv4 address attached, or also can be accessed using the auto-assigned public IPv4.

### EC2 - _Options_
> + On Demand: Fixed rate by hour (by the second) with no commitment.
> + Reserved (Cheaper than ON Demand)
> + Spot
> + Dedicated Hosts: Physical EC2 server dedicated for you use. Dedicated Hosts can help you reduce costs by allowing you to use your existing server-bound software licenses.

### EC2 - _Status Checks_
> + Reboot assumes that instance is in running state and does not change the host. **'impaired'** means **host** is not working properly.
> + If EC2 status checks detect a software or hardwar on the underlying host, it will make the instance(Guest EC2 instances) as **impaired**.
> + For EBS backed instances **stopping and restarting** the instance will cause it to **launch on a new host**, which will fix the problem.
> + For Instance-store backed instances, stopping the instance is not an option, and what you can do is to **_re-launch_ the instance**, but first create an AMI snapshot (or use the last created AMI snapshot) to launch a new instance.

### EC2 - _ENIs_
> + An EC2 instance can have **two** (or **more**, it depends on intances family/type) ENIs in **two different subnets**.
>   + They both and the EC2 instance MUST be in the **same** AZ.
> + By default, network interfaces created "**automatically**" during EC2 instance launch by AWS console, are terminated when the instance is terminated.
>   + **Does not include eth1** that you can add during launch (manually added).
> + Network interfaces created by **CLI** are **"NOT" terminated** **automatically** when the EC2 instance terminates.

### EC2 - Instance Access
> + You have root access with **OpsWorks**, **EC2**, and **Elatic Bean Stalk**, **EMR**.
> + You could only add an IAM at the time you launch an instance.
    + If you wanted to add an IAM role to a running instance, you could not do it.
> + Now you can add an IAM role to a running EC2 instance.
>    + So depending on the exam question choices:
>      + Create a new EC2 instance and add the IAM role at launch time.
>      + You can add the IAM role while the instance is running.
>    + But if you have both but need to select only one.
>    + I would choose the first one, create a new EC2 instance and add ..., because it will still stand true.
>    + Unless if you were asked to select (2), then I would choose both.

### EC2 - _instance Immediate Termination_
Possible reasons that a launched instance immediately terminates are:
> + The instance store-backed AMI you used to launch the instance is missing a required part.
> + You're reached out your EBS volume limit
> + An EBS snapshot is corrupt.

### EC2 Troubleshooting
> + **Connection Timeout Error**
>   + Check if the instance is undergoing a **high CPU load**.
> + **Host Key Not Found Error**
>   + Check that the correct username for the instance AMI has been used in the SSH connection request.
>   + Verify that the connect private way (.pem) file has been used for the connection.
> + **Unprotected Private Key Error**
>   + Verify that the private key has the right permissions.
> + **Insufficient Capacity Error** (When you try to launch an instance or start a stopped instance)
>   + There was no avaliable EC2 capacity to service your request.

### EC2 - Termination Protection
> + You **CAN** terminate instances  with termination protaction enabled.
> + Initiate a shutdown from the Instance Operating System, and instruct AWS to treat it as a terminate.

### EC2 - Reserved Instances
> + You can **NOT** migrate RI instances between regions.
> + They are** Instance Family** specific.
> + They are used  to lower costs.
> + They can have their scope modified from region (default) to AZ or the other way around.
> + They can be used to launch AS Group instances or standalone ones.

### EC2 - IAM roles
+ Basically IAM Roles allow EC2 instances, and applications on EC2 instances, **secure access to another AWS services** such as S3, SQS, SNS, DynamoDB, ...etc.
  + This will avoid having to store secure access credentials on EC2 instances.


### SSD
+ General Purpose SSD - balances price and performance for a wide varity of workloads.
+ Provisioned IOPS SSD - Highest performance SSD volume for mission-critical low-latency or high-throughput workloads.
  
### Magnetic
+ Throughput Optimized HDD - Low cost HDD volume designed for frequently accessed, throughput-intensiv workloads.
+ Cold HDD - Lowest cost HDD volume designed for less frequently accessed workloads. (Cannot be a boot volume)
+ Magnetic - Previous Generation. Can be a boot volume.

### Security Group
+ You **CANNOT** block **specific IP** addresses using **Security Groups**, instead use **Network Access Control Lists.**
+ You can specify allow rules, but not deny rules.

### EBS vs Instance Store
> + All AMIs are categorized as either backed by Amazon EBS or backed by instance store.
> + For EBS Volumes: The root decice from an instance launched from the AMI is an Amazon EBS volume created from an Amazon EBS snapshot.
> + For Instance Store Volumes: The root device for an instance launched from the AMI is an instance store volumne created from a template stored in Amazon S3.
> + Instance Store Volumes are sometimes called **Ephemeral Storage**.
> + Instance store volumes cannot be stopped. If the underlying host fails, you will lose your data.
> + EBS backed instances can be stopped.You will not lose the data on this instance if it is stopped.
> + You can REBOOT both, you will not lose your data.
> + By default, boot ROOT volumes will be deleted on termination, however with EBS volumes, you can tell AWS to keep the root device volumne.
> + EBS snapshohts are backed up to S3 in what manner? (Incrementally)
> + By default EBS volumes are set to 'Delete on Termination', meaning they persist only if instructed.

***
## EBS
### EBS Snapshots - Characteristics
> + Snapshots are **region** specific.
> + Snapshot of encrypted volumes are also encrypted.
> + Creating an EBS volume from an encrypted snapshot will result it an encrypted volume

### EBS Volume Access
> + Snapshots are **not** stored under **customer's own S3 buckets**. But, when you create AMI from instance store, you can view it under customer's own S3 buckets.
> + **EBS volume snapshots** are stored in S3 but **cannot be accessed directly**, you can access them **through EC2 API only**.
> + EBS snapshots stored **incrementally**.
> + EBS snapshots are **asynchronously** created (refers to the fact that **updates** or **changes** to snapshots do not have to happen immediately when the respective volume data changes)

### Using Encrypted/Shared Volumes
+ When have an encrypted snapshot shared with you from another AWS account, and you are granted access permissions to the CMK key used to encrypted the snapshot, you are highly recommended to:
  + Create a copy of the shared snapshot and re-encrypt, during the copy process, using a different CMK key of your own
    + This will protect your access to the snapshot and created volume of it in case: 
      + The key used to encrypt the original shared snapshot is compromised.
      + OR, if the original snapshot owner revokes your rights to the original snapshot encryption key.

### IOPS Performance - Instance Store vs. EBS
+ Use Instance Store instead of EBS-
  + Instance store, although can not provide fro data persistence, but it can provide for **much higher IOPS** compared to, network attached, EBS storage.
  + Remember that, Instance Store is the virtual hard disk space allocated to the instance on the local host.
  + However, note that **not all** new AWS EC2 instance families/types support instance store.

### EBS - Data Encruption at Rest
> + EBS volume encryption is **NOT** supported on some but not all instance types.
> + EBS volume encryption is **NOT** supported on Free tier instances.
> + There are many ways you can encrypt data on an EBS volume at rest, while the volume is attached to an EC2 instance
>   + Use 3rd party EBS volume (SSD or HDD) encryption tools.
>   + Use encrypted EBS volumes
>   + Use encryption at the OS Level (using data encryption plugins/drivers)
>   + Encrypt data at the application level before storing it to the volume.
>   + Use encrypted file system on top of the EBS volume.

### EBS Access - during Snapshot process
+ You can take an EBS volume snapshot while the volume is on a running instance and in use.
  + However, the snapshot will be taken from the data already written to your volume.
    + Any data cached by OS or in memory will not be included.
+ For most accurate snapshot, detach the volume, take the snapshot then re-attach it.

### EBS Data Encryption
> + The actual encryption of data happens on the **EC2 instance**
>   + Remember that the EBS volume is attached to **the EC2 instance over the AWS network**
> + Since data gets encrypted on the EC2 instance, then instnace CPU power **may** or **may not** qualify it to run Encrypted volumes.
>   + For example **T2 Micro instances** do not support encryption
> + Given the above, data between an encrypted volume and EC2 instance is **encrypted in transit** (Encrypted on the EC2 insetance then move to EBS volume).

***
## ASG
### EC2 Status Checks
+ By default, Auto Scaling uses EC2 status checks to determine the status of its launched instance.
  + Any status other than "**running**" will cause the auto scaling process to mark the instance for replacement, terminate it, then launch a new one.
+ If AWS determines a hardware of software problem on a host, it will mark all instances with status "**impaired**" and schedule them for termination.

### AS - Terminating Unhealthy Instances
+ For **Rebalancing**, a new instance is launched first then the one(s) causing the imbalance get terminated.
+ For **unhealthy instances**, Auto Scaling has to terminate the unhealthy first, the launch a new one.

### Merging ASGs
+ To merge existing ASGs, one of them must be updated to span the set of AZs of all groups.
+ Then delete the remaining ASGs (**Except the one that spans all AZs**).
+ The final one (the one that represents the merge) must be one of the original ASGs not a new one. 

### ASG and ELB Troubleshooting
+ Auto Scaling Groups are specific to a **region**.
+ An **Auto Scaling group can be defined in multiple AZs and can scale out or in** in those AZs.

### Dynamic (Event-based) Scaling
> The follwing parameters can influence auto scaling decisions based on their values.
> + The cool down timer
>   + It controls the period of time after a scaling event has happened to the time another scaling event can be initiated (both scale out and scale in are affected).
> + The frequency of the Cloud Watch alarm fetching the monitored object's data
>   + The longer it is, the less potential scaling events, and vice versa.
> + The alarm thresholds based on which a scale-out or scale-in events can be triggered
>   + Lowering the thresholds may cause more scaling out, and increasing the thresholds may cause more scaling in.

### ASG - Launch Configuration via CLI
+ CLI enabled **detailed** monitoring, while AWS Console enabled **basic** monitoring by default.

### Dynamic (Event-based) Scaling
+ The following parameters can influence auto scaling decisions based on their values
  + The cool down timer
    + It controls the period of time after a scaling event has happened to the time another scaling event can be initiated (both scale out and scale in are affected).
  + The frequency of the Cloud Watch alarm fetching the monitored object's data
    + The longer it is, the less potential scaling events, and vice versa.
  + The alarm thresholds based on which a scale-out or scale-in events can be triggered.
    + Lowering the thresholds may cause more scaling out, and increasing the thresholds may cause more scaling-in.

***
## RDS
### RDS
> + Read replicas can be used to scale read operations performance, however:
>   + They can not written to (except in MySQL and MariaDB release 5,6 and later).
>   + You can create snapshots from read replicas.
>   + Asynchronous replication happens from **Primary to read replicas**.
> + AWS RDS is a fully managed service where you can't have access to the OS, or Instance since AWS takes care of all OS, DB, Patching ... etc.
>   + You **only** have access to the **DB engine itself**. 
> + In case of multiple read replicas (MySQL, MariaDB), Promoting one to a standalone DB instance does not affect the other read replicas, those will continue to read from the former primart (source DB instance).
> + **MySQL** and **MariaDB** support read replicas of read replicas.
> + All RDS database engines except MS SQL server can have RDS instance storage capacity up to **6GB**
> + MS SQL DB engine can have storage capcity up to **4TB**
> + Every DB instance has a weekly maintenance window, if you didn't specify one at the time you create the DB instance, AWS will choose one randomly for you (30 mins long).
> + To keep automatic backups enabled, retention period must be set to a **non-zero** value.
> + If you set retention period to zero, automatice backups are disabled, hence, point-in-time recovery which relies on automatic backups' presence, will not function as well.
> + The promoted replica into a standaone DB instance will retain:
>   + Backup retention period
>   + Backup window
>   + DB parameter group
> + You can promote a read replica into a standalone / single AZ database instance
>   + This is true for MySQL, MariaDB, PostgreSQL (MyMaPo)
> + You can specify a point-in-time restore to any given second during the retentaion period.
> + When you initiate a point-in-time recovery, transaction logs are applied to the most appropriate daily backup to restore your DB to that point-in-time.

### RDS - AWARDS events
> + You need to subscribe to Amazon **RDS events** in order to be notified when/if changes occur with a DB instance, DB cluster, DB snapshot, DB cluster snapshot, DB parameter group, or DB security group.
> + You can also use **CloudWathc Alarms and events** to monitor certain metrics, and based on alarms or events take some actions (or notify using SNS).
> + CloudTrail also logs all API calls made to the AWS RDS API.

### RDS - failover in Multi-AZ
+ How can you initate a manual failover from the RDS primary DB Instance to the Standby RDS DB instance in a multi-AZ RDS deployment in an AWS VPC?
  + You need to initiate a reboot with a failover on the primary RDS instance.

***
## S3
### Protact against accidental object deletion for data stored in AWS S3 bucket
+ Enable versioning on a bucket, it ensures that **delete markers** are introduced whenever anyone tries to delete any version.
+ Copying bucket contents into another bucket ensure you have a copy of your data.
+ Bucket policies can allow/deny certain users permissions on your buckets, sub-buckets, or objects.
+ Files can be from 0Bytes to **5TB**.
+ There is **unlimited** storage.
+ Files are stored in **Buckets**.
+ S3 is a universal namespace. That is, **names must be unique globally**.

### Availability VS durablity in different types of S3
| S3 types | Availability | Durablity |
| ------ | ----------- | ----------- |
| S3 standard storage class | 99.99% | 99.999999999% |
| S3 Infrequent Access (S3-IA) | 99.9 %| %99.999999999% |
| S3 Reduced Redundancy storage class (S3-RRS) | 99.99% | 99.99% |
| Glacier storage | No SLA or Availability guarantees | 99.999999999% |

### Glacier
> + Three archive retrieval methods can be used to restore archives from Glacier
>   + Expedited
>   + Standard
>   + Bulk
> + **Synchronous** Upload and **Asynchronous** Retrieval

### S3 Security
> + You can use _access control mechanisms_ such as **bucket policies** and **Access Control Lists** (ACLs) to selectively grant permissons to users and groups of users.
> + You can also securly upload/download your data to Amazon S3 via **SSL** endpoints using the HTTPS protocol, or by using **Server-Side Encryption (SSE)** or **Client-Side Encryption**.
> + **Access log** records can be used for audit purposes and contain details about the request, such as the request type, the resources specified in the request, and the time and date the request was processed.
> + By **AWS CloudTrail Data Events**, customers who need to capture IAM/user identity iinformation in their logs.

### Options for encrypting data stored on Amazon S3
> + **SSE-S3** : It's provides an intergated solution where Amazon handles key management and key protection using multiple layers of security.
> + **SSE-C** : Enables you to leverage Amazon S3 to perform the encryption and decryption of your objects while retaining control of the keys used to encrypt objects.
> + **SSE-KMS**: Enables you to use **AWS Key Management Service** (AWS KMS) to manage your encryption keys.
> + **Using an encryption client library**: Such as the Amazon S3 Encryption Client, you retain control the keys and complete the encryption and decyption of objects client-side using an encryption library of your choice.

### S3 bucket properties
+ Versioning
+ Server Access Logging
+ Static Web Hosting
+ Object-level logging
+ Tags
+ Transfer acceleration
+ Events

### DELETE Object
+ The **DELETE operation** removes the null version (if there is one) of an object and inserts a **delete marker**, which becomes the current version of the object. If there isn't a null version, Amazon S3 does not remove any objects.

### AWS Storage Gateway
> + **File Gatway**: A file gateway supports a file interface into Amazon Simple Storage Service (Amazon S3) and combines a service and a virtual software appliance. 
> + Volume Gateway: A volume gateway provides cloud-backed storage volumes that you can mount as Internet Small Computer System Interface (iSCSI) devices from your on-premises application servers. The gateway supports the following volume configurations:
  + Cached volumes (frequently accessed data)
  + Stored volumes (low-latency access)
> + Tape Gateway: With a tape gateway, you can cost-effectively and durably **archive backup** data in Amazon **Glacier**. 

### Amazon S3 Data Consistency Model
> + Amazon S3 provides **read-after-write consistency** for **PUTS** of **new** objects in your S3 bucket in all regions with one caveat. 
> + Amazon S3 offers **eventual consistency for overwrite PUTS and DELETES** in all regions.

### Minimum and Maximum file sizes that can be stored in S3 respectively: **0 (not 1) Bytes** and **5 terabytes**

### How Do I Undelete a Deleted S3 Object?
> + To be able to undelete a deleted object, you must have **had versioning enabled** on the bucket that contains the object before the object was deleted.
> + When you delete an object in a versioning-enabled bucket, all versions remain in the bucket and Amazon S3 creates a **delete marker for the object**. To undelete the object, you must delete this delete marker. 
> + When versioning is enabled, a simple DELETE **cannot** permanently delete a object.
> + To **permanently** delete versioned objects, you must use **DELETE** Object **versionID**.


***
## AWS API Gateway
+ It automatically protects your backend system from distributed _denial-of-service (DDos)_ attacks.
+ Reduced Latency and Distributed Denial of Service (DDos) protection through the use of CloudFront. (NOTE: _DDos_ is **NOT build-in** feature)
+ SDK generation for iOS, Android, and Javascript.

_**API Gateway Caching**_
You can enable API caching in Amazon API Gateway to cache your endpoint's responses.
> When enabling AWS API caching for API Gateway through console, you can make following actions:
> + Cache capacity
> + Encrypt cache Data
> + Flush entire cache

_**API Gateway Throtting**_
Amazon API Gateway usage plans Now support **Method Level Throtting**. (每个 Method 都可以定义自己的Throtting settings)

_**API Gateway Resouce Policy**_
It's a JSON policy documents that you attach to an API to control whether a specified pricipal (typically an IAM user or role) can invoke the API.

_**API Gateway Stage variables**_
Stage variables are name-value pairs that you can defines as configuration attributes associated with a deployment stage of an API.

_**API Gateway Access Logging**_
In API Gateway access logging, you, as an API developer, want to log who has accessed your API and how the caller accessed the API.

_**Controlling Access to an API in API Gateway**_
> + Resouce policies
> + Standard AWS IAM roles and policies
> + Cross-origin resources sharing (CORS)
> + Lambda authorizers
> + Amazon Cognito user pools -- _As an alternative to using **IAM roles** and **policies** or **Lambda authorizers**, you can use an Amazon **Cognito user pool** to control who can access your API in Amazon API Gateway._
> + Client-side SSL certificates
> + Usage plans

***
## Lambda
### Lambda - Event sources
+ AWS Lambda supports many AWS services as event sources.
+ **Event sources** maintain the event source mapping, except for the stream-based services (Amazon Kinesis Streams and Amazon DynamoDB streams).
  + For the stream-based services, AWS Lambda maintains the event source mapping, and Lambda function performs polling.
+ **Event sources that aren't stream-based**
  + If you create a Lambda function to process events from the event sources that aren't stream-based. Each published event it a unit work, in parallel, up to your account limits.

***
## Redshift
+ Support encrption of data "at rest" using hardware accelerated AES-256 bits
  + By default, AWS Redshift takes care of encryption key management
  + You can choose to manage your own keys through HSM (Hardware Security Modules), or AWS KMS (Key Management Service).
+ Support SSL Encryption in-transit between client applications and Redshift data warehouse cluster.
+ You cannot have direct access to your AWS Redshift cluster nodes, however, you can through the applications themselves.
+ Redshift can NOT ingest a large amount of data in real time (Kinesis can do this). 

*** 
## Kinesis
Ad hoc query/analysis is a business intelligence process designed to answer a single, specific business question.
+ This allows for quicker response times when a business question comes up, which in turn should help the user respond to issues and make business decisions faster.
+ The produce of ad hoc analysis is typically a statistical model, analytic report, or other type of data summary.
+ Ad hoc analysis may be used to create a report that does not already exist, or drill deeper into a static report tot get details about accounts, transactions, or records.
+ Redshift is a columnar analytical data warehouse (analytical database).
  + It isn't designed to be continuously loaded, rather, a small number of concurrent queries performing large scans of data.
  + Not optimized for ad hoc querying.

***
## Route 53
### NS Records
NS stands for Name Server records. They are used by Top Level Domain servers to direct traffic to the Content DNS server which contains the authoritative DNS records.

### A Records
AN "A" record is the fundamental type of DNS record. The "A" in A record stands for "Address". The A record is used by a computer to translate the name of the domain to an IP address.

### TTL
The length that a DNS record is cached on either the Resolving Server or the users own local PC is equal to the value of the "Time To Live" (TTL) in seconds. The lower the time to live, the faster changes to DNS records take to propagate throughout the internet.

## CNAMES
A Canoical(权威性) Name (CName) can be used to resolve one domain name to another. For example, you may have a mobile website with the domain name http://m.acloud.guru that is used for when users browse to your domain name on their mobile devices. You may also want the name http://mobile.acloud.guru to resolve to this same address.

### Alias Records
+ Alias records are used to map resouce record sets in your hosted zone to Elastic Load Balancers, CloudFront distributions, or S3 buckets that are configured as websites.
+ Alias records work like a CNAME record in that you can map one DNS name (www.example.com) to another 'target' DNS name (elb1234.elb.amazonaws.com).
+ **Key difference** - A CNAME can't be used for naked domain names (zone apex record). You can't have a CNAME for http://acloud.guru, it must be either an A record or an Alias.
+ Given the choice, always choose an Alias Record **over** a CNAME.

***
## ELB
### ELB - _Monitoring_
ELB monitoring can be achieved by:
  + AWS Cloud Watch:
    + AWS ELB service sends ELB metrics to cloud watch every "One minute"
    + ELB service sends these metrics only if there are requests flowing through the ELB
    + AWS Cloud Watch can be used to trigger an SNS notification if a threshold you define is reached
  + Access logs:
    + Disabled by default
    + You can obtain request information such as requester, time of request, requester IP, request type ... etc.
    + Optional (disabled by default), you can choose to store the access logs in an S3 bucket that you specify.
    + You are not charged extra for enabling access logs
      + You pay for S3 storage.
    + You are not charged for data transfer of access logs from ELB to the S3 bucket.
  + AWS Cloud Trail
    + You can use it to capture all API calls for your ELB
    + You can store these logs in an S3 bucket that your specify

### HTTPS/SSL Security Policy
+ If you do not specify otherwise, AWS Elastic Load Balancing service will configure your ELB with the current/latest pre-defined security policy.
+ For Frontend (Client to ELB) [HTTPS/SSL]
  + You can define your custom security policy or use the ELB pre-defined security policies.
+ For backend, encrypted, connections
  + Pre-defined security policies are always used.
+ Server Order Preference
  + If enabled (default for default security policies), the first match in the ELB cipher list with the Client list is used.

### Session Affinity - ELB Sticky sessions
+ For application cookie sticky sessions:
  + If the cookie did not expire, but the backend instance becomes unhealthy, the ELB will route the traffic to a new, healthy, instance and keep the session stickiness to the new instance, even if the former one comes back healthy again.
+ For ELB, duration based, cookie stickiness:
  + If the backend instance to which a session was sticky, fails to becomes unhealthy, the ELB routes the new session/requests (that were stuck before) to a new, healthy, instance and the session is no longer a sticky one.
+ If the ELB is configured with session affinity (sticky sessions), it will continue to route the requests from the same clients to the same backend EC2 instances disregarding:
  + How loaded these backend EC2 instances might be
  + Whether there are new backend instances added to distribute the load.

### Originator/Requester Details
+ To allow the backend EC2 (Web layer) Instances to know the originator/requester details including, source IP address and port, Destination IP address and port, you can:
  + Enable Proxy Protocol for TCP/SSL Layer 4 listners as supported on the ELB
  + Enable X-Forewaded-For headers for HTTP/HTTPS listeners on the ELB.

### SSL Security Policy Components
+ SSL protools
  + SSL or TLS, are crytographic protocols.
+ SSL Ciphers (a set of ciphers is called a cipher suite)
  + Encryption algorithms
  + SSL can use different ciphers to encrypt data
+ Server Order Preference
  + If enabled, the first match in the ELB cipher list with the Client list is used.
  + If disabled (Default), first match in the client cipher list with the ELB list is used.

### ELB
+ By default, an ELB enabled to load balance among multiple AZs, will distribute the traffic among the AZ evenly, disregarding the number of registered backend EC2 instance in each AZ.
+ Enabling ELB cross zone load balacning (disabled by default) ensures that the ELB will distribute traffic among the multi AZ registered backend EC2 instances evently.
+ ELB is a Region specific AWS Service.
+ You can't load balance across regions using ELB alone.
+ All that it takes to enable an ELB in an AZ (in the same region as the ELB), is to enable a subnet for the ELB in that AZ.
  + If the ELB is to function as internet-facing, the subnet must be a public subnet.
+ A stateless application, is an application that does not maintain the session state locally on its file system.
  + Stateless applications are required to scale horizontally using ELBs and Auto scaling (adding/removing EC2 instances as the load increases/decreases and using ELB to distribute load).
    + You can use DynamoDB (given its scalability) for storing detailed user session information (application writes to the DynamoDB table).
    + And you can use EFS and S3 to store larger file uploads from the users, or interim batch processing results..etc
    + You can also, in case the application has workflow steps that needs to be tracked, instead of saving this on the EC2 local files system (application), it is bettter to use SWF services to track the workflows.
+ Adding Auto Scaling group to the application web/app tier would have been a perfect plus to the architecture, but since it was included as an option, we selected the closet one the perfect solution.
  + A perfect H.A solution that will avoid any single point of failure, for such a scenrio, would include:
    + VPC (with properly configure sec groups and N ACLs) in AWS with IGW configured and attached.
    + Two AZs in the same region (at least two).
    + Public subnet(s) in each AZ, ELB defined on one of them to enable it to serve the AZ.
    + Private subnet for the database tier (to protect it)
    + Multi-AZ RDS or AWS managed DB engine
    + Auto scaling defined in both AZs and configured to work with the ELB and EC2 instances.
+ Remeber, similar to Multi-AZ RDS, ELB when deployed it is redundant and what takes care of this is the AWS ELB Service itself.
  + If you need to have an ELB in an H.A solution, you do not need to configure two seperate ELBs yourself to guarantee HA.
    + AWS will take care of this.
  + A scenario where you would need to configure two ELBs, is if you want to load balance in a different Region, thus you configure one ELB in each Region, and have Route53 do a load distribution (through DNS resolution policy/routing) between the two.

### ELB - SSL Protocols
+ The ELB supports the following SSL protocols:
  + TLS 1.0
  + TLS 1.1
  + TLS 1.2
  + SSL 2.0
  + It does not support TLS 1.3 or SSL 2.0 (which is deprecated).

### ELB - Connection Draining
+ Is disabled by default
+ When enabled, the ELB when identifying unhealthy instances, it will wait for a period of 300 seconds (by default), for the in-flight sessions to this EC2 back end instance to complete
  + If the in-flight sessions are not completed before the maximum time (300 seconds configurable between 1 - 3600 seconds), the ELB will force termination of these sessions.
  + During the connection draining, the back end instance state will be "InService: Instance Deregistration Currently In Progress".
  + AWS Auto-Scaling would also honour the connection draining setting for unhealthy instances.
  
### ELB - Pre-Warming
+ ELB Scaling
  + The time required for the Elastic Load Balancing to detect the increase in traffic/load and scale (or add) more ELB nodes can take from 1 to 7 minutes according to traffic profile.
    + ELB is not designed to queue request.
      + It will return Error 503 (HTTP) if it can't handle the request, any requests above the ELB capacity will fail.
    + ELB service can scale and keep up with traffic increase, if you traffic increases at 50% in step or linear form every 5 mins.
+ ELB Pre-Warm
  + If your traffic increases faster than this, you need to contact AWS to pre-warm ELB nodes for your traffic needs.
