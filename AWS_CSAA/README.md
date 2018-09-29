# AWS CSAA Study Notes

## VPC

### Security Group - _Settings_
+ In _**Default**_ Group
  - **DO not**  have explicit **deny** rules.
  - All **outbound** traffic is **allowed** by default.
  - By default there is a **0.0.0.0/0** on All protocals/ports allowed **by default - outbound**.
  - Inbound rules allowing Instance assigned the same security group to talk to one another.

+ In _**Custom**_ Group
  + All **outbound** traffic is **allowed** by default.
  + All **inbound** traffic is **denied by default** in a **Custom** security group.

### Security Group - _Configurations_
+ Configuring a VPC using the VPC wizard that has a VPN-Only and VPN Access implies:
  + No public subnet
  + No NAT instance/gateway
  + It will create a Virtual Private Gateway (VGW) but without an Elastic IP
  + It will create a VPN connection

### Direct Connect (DX)
> + It is a direct connection (**not internet based**) **and provides for higher speeds (bandwidth), less lantency and higher performance** than Internet.
> + A Virtual Interface (VIF) is basically a 802.1Q VLAN mapped from the customer router to the Direct Conncet router.
> + You need one private VIF to connect to your private VPC subnets, and one public VIF to connect your AWS public services.
> + You can NOT establish layer 2 over your DX connection.
> + You can NOT use a NAT instance /gateway in your VPC over the direct connection connection.
> + VPN will **NOT** provide bandwidth and latency guarantees.

### AWS Console - Deleting a VPC
> + When you launch a VPC with **public, VPN only subnets and a hardware VPN**, the following also gets created:
>   + VGW
>   + VPN connection is defined and created
> + You can delete this VPC, everything will be deleted except VGW and VPM connection.
>   + VGW will be detached from the VPC
>   + VPN Connection will still be using the VGW

### VPC Wizard Configurations
> + Target "ALL" is not a valid Target for Route tables.
> + This is the entry that is created by default in "any" route table to allow VPC subnets to communicate with one another.
> + The VPC will have an Internet Gateway (IGW) created and attached.
> + The **custom route** table will have an empty pointing at the VPC IGW as a target for 0.0.0.0/0 for Internet Access.
> + For VPC Wizard configuartions, when a public and private subnets are created, **two route tables** are created by AWS for you.
  >>  - **Private** IPv4 subnet ---> **Main** VPC route table
  >>  - **Public** IPv4 subnet ---> **Custom** route table

### Launching EC2 instances in a VPC
> When launching an EC2 instance in a VPC you can:
> + Leave it to AWS to select the best AZ for your EC2 instance
> + Or, specify which AZ you want your EC2 instance launched in
> + Also, you can leave it to AWS to assign an IP address for your instance
> + Or, you can assign the IP Address (if you choose the subnet for the EC2 instance) in the subnet where you want to launch your EC2 instance

### Designing VPC Subnets
+ TO configure an ELB to load balance to instances in a subnet in a specific AZ, the **ELB must have one _public_ subnet** defined for the ELB in that AZ.
+ Web instances that are being load balanced in an AZ can be on **public** or **private** subnets.

### AWS Direct Connect Virtual Interface
+ You **must** create a **virtual interface** to begin using your AWS Direct Connect connection. 
+ You can create a **private virtual interface** to connect to your VPC, or you can create a **public virtual interface** to connect to AWS services that aren't in a VPC, such as Amazon S3 and Amazon Glacier.
+ You can configure **multiple virtual interfaces** on a single AWS Direct Connect connection.
+ For private virtual interfaces, you need one private virtual interface for each VPC to connect to from the AWS Direct Connect connection, or you can use a Direct Connect gateway.


### VPC Endpoint
> + Enables creation of a private connection between your VPC and another AWS service using its private IP Address. It currentl supports endpoints for **S3** and **Dynamo DB**.
> + Endpoints are supported within the **same region only**.You cannot create an endpoint between a VPC and a service in a different region.
> + VPC endpoint always **takes precedence over** NAT Gateway or Internet Gateway. In the absence of VPC endpoint, request to S3 are routed to NAT Gateway or Internet Gateway **based on** their existence in route table.

### VPC Endpoint Policies
> + A VPC endpoint policy is an **IAM resource polic**y that you attach to an endpoint when you create or modify the endpoint. 

### NAT Instance as a Proxy
> + NAT instance in **VPC** can be used with a public IP or Elastic IP address.
> + Does a PAT function, so it can hide more than one device/EC2 instance behind it when the traffic is initiated from these instances behind the NAT instance.
> + **NAT instance is a user responsibility and is not a managed AWS service (Unlike the NAT gateway).**
> + NAT instances can be deployed accross AZ's independetly for HA. EC2 instances, in each AZ, on private subnets requiring NAT should be configured to use the local NAT instance in the respective AZ.

### NAT Gateways
> + You can use a network address translation (NAT) gateway to enable instances in a **private subnet** to connect to the internet or other AWS services, but **prevent the internet** from initiating a connection with those instances.
> + NOT Support IPv6.
> + Internet Gateway are **two-way** traffic.
> + When creating NAT Gateway, there is an option to select subnet in which NAT Gateway will be created. This must be a public subnet which has a route to internet through internet Gateway. If a private subnet is selected when creating NAT Gateway, it cannot route traffic to internet and hence the requests would fail.
> + NAT Gateway doesn't have **security groups**.

### Route table
> + Each subnet in your VPC **must be associated** with a route table.
> + A subnet can only be associated with one route table at a time, but you can associate multiple subnets with the same route table.
> + Any route table _local route_ **cannot** be **edited** or **deleted**.

### Bastion Host
> + Bastion hosts are instances that sit within your public subnet and are typically accessed using SSH or RDP.

### VPC Flow Logs
> + VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC.
> + Flow log data can be published to Amazon CloudWatch Logs and Amazon S3.
> + After you've created a flow log, you can retrieve and view its data in the chosen destination.


***
## EC2
### EC2 Enhanced Networking
> + Enhanced networking is **not** supported on **all** EC2 instances
> + Enhanced networking does NOT cost extra
> + Enhanced networking can be enabled on **Instance-store backed** or **EBS-backed EC2 instances**.
> + Enhanced networking **requirements**:
>   + Instances be launched from an **HVM AMI** (no PV)
>   + Is ONLY supported **in a VPC**

### EC2 - Placement Groups
> + Is a feature that allows involved EC2 instances to communicate via **high bandwidth** and **low latency** connections within an AZ.
> + Placement groups **must** have all instances in the **same** AZ (only one single)
> + You can use **Enhanced networking instances** in **Placement groups**.
> + A placement group is ideal for EC2 instances that require high network throughout and low latency across a single AZ.

### EC2 - Bastion Host
> + Bastion host needs to be deployed in a public subnet
> + You can allow RDP or SSH or both based on the Windows or Linux instances you need to manage respectivly
> + Bostion host can have an Elastic IPv4 address attached, or also can be accessed using the auto-assigned public IPv4.

### EC2 Options
> + On Demand: Fixed rate by hour (by the second) with no commitment.
> + Reserved (Cheaper than ON Demand)
> + Spot
> + Dedicated Hosts: Physical EC2 server dedicated for you use. Dedicated Hosts can help you reduce costs by allowing you to use your existing server-bound software licenses.

### EC2 - Status Checks.
> + Reboot assumes that instance is in running state and does not change the host. **'impaired'** means **host** is not working properly.
> + If EC2 status checks detect a software or hardwar on the underlying host, it will make the instance(Guest EC2 instances) as **impaired**.
> + For EBS backed instances **stopping and restarting** the instance will cause it to **launch on a new host**, which will fix the problem.
> + For Instance-store backed instances, stopping the instance is not an option, and what you can do is to **re-launch** the instance, but first create an AMI snapshot (or use the last created AMI snapshot) to launch a new instance.

### EC2 - ENIs
> + An EC2 instance can have **two** (or **more**, it depends on intances family/type) ENIs in **two different subnets**.
>   + They both and the EC2 instance MUST be in the same AZ.
> + By default, network interfaces created "**automatically**" during EC2 instance launch by AWS console, are terminated when the instance is terminated.
>   + **Does not include eth1** that you can add during launch (manually added).
> + Network interfaces created by **CLI** are **"NOT" terminated** **automatically** when the EC2 instance terminates.

### EC2 - Instance Access
+ You have root access with **OpsWorks**, **EC2**, and **Elatic Bean Stalk**, **EMR**.
+ You could only add an IAM at the time you launch an instance.
  + If you wanted to add an IAM role to a running instance, you could not do it.
+ Now you can add an IAM role to a running EC2 instance.
  + So depending on the exam question choices:
    + Create a new EC2 instance and add the IAM role at launch time.
    + You can add the IAM role while the instance is running.
  + But if you have both but need to select only one.
    + I would choose the first one, create a new EC2 instance and add ..., because it will still stand true.
    + Unless if you were asked to select (2), then I would choose both.

### EC2 instance Immediate Termination
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
> 
+ 
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


