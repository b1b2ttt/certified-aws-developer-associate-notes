# VPC: Virtual Private Cloud

Within a region, you’re able to create VPCs. Each VPC contain subnets (networks). Each subnet must be mapped to an AZ. It’s common to have a public ip and private ip subnet. It’s common to have many subnets per AZ.

#### Internet Gateway & NAT Gateways
- Internet Gateways helps our VPC instances connect with the internet
- Public Subnets have a route to the internet gateway.
- NAT Gateways (AWS-managed) and NAT Instances (self-managed) allow your instances in your Private Subnets to access the internet while remaining private

#### Network ACL & Security Groups
- NACL (Network ACL)
	- A firewall which controls traffic from and to subnet
	- Can have ALLOW and DENY rules
	- Are attached at the Subnet level
	- Rules only include IP addresses
- Security Groups
	- A firewall that controls traffic to and from an ENI / an EC2 Instance
	- Can have only ALLOW rules
	- Rules include IP addresses and other security groups

#### VPC Flow Logs
- Capture information about IP traffic going into your interfaces: 
	- VPC Flow Logs
	- Subnet Flow Logs
	- Elastic Network Interface Flow Logs
- Helps to monitor & troubleshoot connectivity issues. Example: 
	- Subnets to internet
	- Subnets to subnets
	- Internet to subnets
- Captures network information from AWS managed interfaces too: Elastic Load Balancers, ElastiCache, RDS, Aurora, etc...
- VPC Flow logs data can go to S3 / CloudWatch Logs

#### VPC Peering
Connect two VPC, privately using AWS’ network
Make them behave as if they were in the same network
Must not have overlapping CIDR (IP address range)
VPC Peering connection is not transitive (must be established for each VPC that need to communicate with one another)

#### VPC Endpoints
- Endpoints allow you to connect to AWS Services using a private network instead of the public www network
- This gives you enhanced security and lower latency to access AWS services
- VPC Endpoint Gateway: S3 & DynamoDB
- VPC Endpoint Interface: the rest

#### Site to Site VPN & Direct Connect
- Site to Site VPN
	- Connect an on-premises VPN to AWS
	- The connection is automatically encrypted 
	- Goes over the public internet
- Direct Connect (DX)
	- Establish a physical connection between on-premises and AWS
	- The connection is private, secure and fast
	- Goes over a private network
	- Takes at least a month to establish
- Note: Site-to-site VPN and Direct Connect cannot access VPC endpoints

#### VPC Closing Comments
- VPC: Virtual Private Cloud
- Subnets: Tied to an AZ, network partition of the VPC
- Internet Gateway: at the VPC level, provide Internet Access
- NAT Gateway / Instances: give internet access to private subnets
- NACL: Stateless, subnet rules for inbound and outbound
- Security Groups: Stateful, operate at the EC2 instance level or ENI
- VPC Peering: Connect two VPC with non overlapping IP ranges, non transitive
- VPC Endpoints: Provide private access to AWS Services within VPC
- VPC Flow Logs: network traffic logs
- Site to Site VPN: VPN over public internet between on-premises DC and AWS 
- Direct Connect: direct private connection to a AWS

#### Public Subnets usually contain:
* Load Balancers
* Static Websites
* Files
* Public Authentication Layers

Private Subnets usually contain:
* Web application servers
* Databases

Public and Private subnets can communicate if they’re in the same VPC

#### AWS VPC Summary
* VPC & Regions aren’t much asked at the developer associate exam
* All new accounts come with a default VPC
* It’s possible to use a VPN to connect to a VPC
* VPC flow logs allow you to monitor the traffic within, in and out of your VPC (useful for security, performance, audit)
* VPC are per Account per Region
* Subnets are per VPC per AZ
* Some AWS resources can be deployed in VPC while others can’t
* You can peer VPC (within or across accounts) to make it look like they’re part of the same network

