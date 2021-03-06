# ELB: Elastic Load Balancers

#### High Availability and Scalability For EC2
- Vertical Scaling: Increase instance size (= scale up / down) 
	- From: t2.nano - 0.5G of RAM, 1 vCPU
	- To: u-12tb1.metal – 12.3 TB of RAM, 448 vCPUs
- Horizontal Scaling: Increase number of instances (= scale out / in) 
	- Auto Scaling Group
	- Load Balancer
- High Availability: Run instances for the same application across multi AZ
	- Auto Scaling Group multi AZ
	- Load Balancer multi AZ

#### What is load balancing?
Load balancers are servers that forward internet traffic to multiple servers (EC2 Instances) downstream

#### Why use a load balancer?
* Spread load across multiple downstream instances
* Expose a single point of access (DNS) to your application
* Seamlessly handle failures of downstream instances
* Do regular health checks to your instances
* Provide SSL termination (HTTPS) for your websites
* Enforce stickiness with cookies
* High availability across zones
* Separate public traffic from private traffic

#### AN ELB (EC2 Load Balancer) is a managed load balancer
* AWS guarantees that it will be working
* AWS takes care of upgrades, maintenance, high availability
* AWS provides only a few configuration knobs

It costs less to setup your own load balancer but it will be a lot more effort on your end. It is integrated with many AWS offerings / services

#### Types of load balancers on AWS
* Classic Load Balancer (v1 - older generation - 2009)
* Application Load Balancer (v2 - new generation - 2016)
* Network Load Balancer (v2 - new generation - 2017)
* You can setup internal or external ELBs

#### Health Checks
* Health checks are crucial for load balancers
* They enable the load balancer to know if instances it forwards traffic to are available to reply to requests
* The health check is done on a port and a route (/health is common)
* If the response is not 200 (OK), then the instance is unhealthy

#### Classic Load Balancers(v1)
- Supports TCP (Layer 4), HTTP & HTTPS (Layer 7)
- Health checks are TCP or HTTP based
- Fixed hostname XXX.region.elb.amazonaws.com

work flow:
	 	 listener		 internal
client --------->CLB----------->EC2

#### Application Load Balancer (v2)
* Application load balancers (Layer 7) allow to do:
  * Load balancing to multiple HTTP applications across machines (target groups)
  * Load balancing to multiple applications on the same machine (ex: containers)
  * Load balancing based on route in URL
  * Load balancing based on hostname in URL 
* Basically, they’re awesome for micro services & container-based application (example: Docker & Amazon ECS) 
* Has a port mapping feature to redirect to a dynamic port 
* In comparison, we would need to create one Classic Load Balancer per application before.That was very expensive and inefficient!
* Good to Know
    * Stickiness can be enabled at the target group level
        * Same request goes to the same instance
        * Stickiness is directly generated by the ALB (NOT the application)
    * ALB supports HTTP/HTTPS & Web sockets protocols
    * The application servers don’t see the IP of the client directly
        * The true IP of the client is inserted in the header X-Forwarded-For
        * We can also get Port (X-Forwarded-Port) and protocol (X-Forwarded-Proto)
		
#### Network Load Balancer (v2)
* Layer 4 allow you to do:
    * Forward TCP traffic to your instances
    * Handle millions of requests per second
    * Support for static IP or elastic IP
    * Less latency ~100ms (vs 400 ms for ALB)
* NLB has ** one static IP ** per AZ, and supports assigning Elastic IP (helpful for whitelisting specific IP)
* Network Load Balancers are mostly used for extreme performance and should not be the default load balancer you choose
* Overall, the creation process is the same as the Application Load Balancer

#### Cross-Zone Load Balancing
-  With Cross Zone Load Balancing: each load balancer instance distributes evenly across all registered instances in all AZ
-  Otherwise, each load balancer node distributes requests evenly across the registered instances in its Availability Zone only.
- Classic Load Balancer
	- Disabled by default
	- No charges for inter AZ data if enabled
- Application Load Balancer
	- Always on (can’t be disabled) 
	- No charges for inter AZ data
- Network Load Balancer
	- Disabled by default
	- You pay charges ($) for inter AZ data if enabled

#### Load Balancers Good to Know
* Any Load Balancer (CLB, ALB, NLB) has a static host name. They do not resolve and use underlying IP
* LBs can scale but not instantaneously - contact AWS for a “warm up”
* NLB directly see the client IP
* 4xx errors are client induced errors
* 5xx errors are application induced errors
    * Load balancer Errors 503 means at capacity or no registered target
* If the LB can’t connect to your application, check your security
- Monitoring
	-  ELB access logs will log all access requests (so you can debug per request)
	-  CloudWatch Metrics will give you aggregate statistics (ex: connections count)
	
#### SSL/TLS - Basics
- An SSL Certificate allows traffic between your clients and your load balancer to be encrypted in transit (in-flight encryption)
- SSL refers to Secure Sockets Layer, used to encrypt connections
- TLS refers toTransport Layer Security,which is a newer version
- Nowadays, TLS certificates are mainly used, but people still refer as SSL
- Public SSL certificates are issued by Certificate Authorities (CA)
- SSL certificates have an expiration date (you set) and must be renewed

#### Load Balancer - SSL Certificates
• The load balancer uses an X.509 certificate (SSL/TLS server certificate) • You can manage certificates using ACM (AWS Certificate Manager)
• You can create upload your own certificates alternatively
• HTTPS listener:
	• You must specify a default certificate
	• You can add an optional list of certs to support multiple domains
	• Clients can use SNI (Server Name Indication) to specify the hostname they reach
	• Ability to specify a security policy to support older versions of SSL /TLS (legacy clients)

#### SSL – Server Name Indication (SNI)
- SNI solves the problem of loading multiple SSL certificates onto one web server (to serve multiple websites)
- It’s a “newer” protocol, and requires the client to indicate the hostname of the target server in the initial SSL handshake
- The server will then find the correct certificate, or return the default one
- Note:
	- Only works for ALB & NLB (newer generation), CloudFront
	- Does not work for CLB (older gen)


#### Elastic Load Balancers – SSL Certificates
- Classic Load Balancer (v1)
	- Support only one SSL certificate
	- Must use multiple CLB for multiple hostname with multiple SSL certificates
- Application Load Balancer (v2)/ Network Load Balancer (v2)
	- Supports multiple listeners with multiple SSL certificates 
	- Uses Server Name Indication (SNI) to make it work

#### Connection Draining
- Feature naming:
	- CLB:ConnectionDraining
	- TargetGroup:DeregistrationDelay (for ALB and NLB)
- Time to complete “in-flight requests” while the instance is de-registering or unhealthy
- Stops sending new requests to the instance which is de-registering
-  Between 1 to 3600 seconds, default is 300 seconds
-  Can be disabled (set value to 0)
-  Set to a low value if your requests are short
