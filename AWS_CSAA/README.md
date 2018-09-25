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
## S3
### Protact against accidental object deletion for data stored in AWS S3 bucket
+ Enable versioning on a bucket, it ensures that **delete markers** are introduced whenever anyone tries to delete any version.
+ Copying bucket contents into another bucket ensure you have a copy of your data.
+ Bucket policies can allow/deny certain users permissions on your buckets, sub-buckets, or objects.

### Availability VS durablity in different types of S3
| S3 types | Availability | Durablity |
| ------ | ----------- | ----------- |
| S3 standard storage class | %99.99 | %99.999999999 |
| S3 Infrequent Access (S3-IA) | %99.9 | %99.999999999 |
| S3 Reduced Redundancy storage class (S3-RRS) | %99.99 | %99.99 |
| Glacier storage | No SLA or Availability guarantees | %99.999999999 |

### Glacier
> + Three archive retrieval methods can be used to restore archives from Glacier
>   + Expedited
>   + Standard
>   + Bulk
> + **Synchronous** Upload and **Asynchronous** Retrieval
>  

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


