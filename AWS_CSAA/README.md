# AWS CSAA Study Notes

## VPC

### Security Group Settings
+ In Default Settings
  - **DO not**  have explicit **deny** rules.
  - All **inbound** traffic is **denied by default** in a **Custom** security group.
  - By default there is a **0.0.0.0/0** on All protocals/ports allowed **by default - outbound**.

### Security Group Configurations
+ Configuring a VPC using the VPC wizard that has a VPN-Only and VPN Access implies:
  + No public subnet
  + No NAT instance/gateway
  + It will create a Virtual Private Gateway (VGW) but without an Elastic IP
  + It will create a VPN connection
