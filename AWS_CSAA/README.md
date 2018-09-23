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
  + 

### Security Group - _Configurations_
+ Configuring a VPC using the VPC wizard that has a VPN-Only and VPN Access implies:
  + No public subnet
  + No NAT instance/gateway
  + It will create a Virtual Private Gateway (VGW) but without an Elastic IP
  + It will create a VPN connection


### **VPC Endpoint**
Enables creation of a private connection between your VPC and another AWS service using its private IP Address. It currentl supports endpoints for **S3** and **Dynamo DB**.


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


