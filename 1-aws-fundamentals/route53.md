# Route 53

Route 53 is a managed DNS (Domain Name System)

DNS is a collection of rules and records which helps clients understand how to reach a server through URLs.

In AWS, the most common records are (will be on exam):
* A: URL to IPv4
* AAAA: URL to IPv6
* CNAME: URL to URL
* ALIAS: URL to AWS resource

Route 53 can use:
* Public domain names you own
* Private domain names that can be resolved by your instances in your VPCs

Route53 has advanced features such as:
* Load balancing (through DNS - also called client load balancing)
* Health checks (although limited…)
* Routing policy: simple, failover, geolocation, geoproximity, latency, weighted

CNAME vs Alias
- AWS Resources (Load Balancer, CloudFront...) expose an AWS hostname:
- lb1-1234.us-east-2.elb.amazonaws.com and you want myapp.mydomain.com
- CNAME:
	- Points a hostname to any other hostname. (app.mydomain.com => blabla.anything.com) 
	- ONLY FOR NON ROOT DOMAIN (aka. something.mydomain.com)
- Alias:
	- Points a hostname to an AWS Resource (app.mydomain.com => blabla.amazonaws.com)
	- Works for ROOT DOMAIN and NON ROOT DOMAIN (aka mydomain.com)
	- Free of charge
	- Native health check


Prefer Alias over CNAME for AWS resources (for performance reasons)

Routing Policy
- Simple Routing Policy: Maps a hostname to another hostname
- Weighted Routing Policy: Control the % of the requests that go to specific endpoint
- Latency Routing Policy: Redirect to the server that has the least latency close to us
- Failover Routing Policy
- Geo Location Routing Policy: This is routing based on user location
- Multi Value Routing Policy:
	- Use when routing traffic to multiple resources
	- Want to associate a Route 53 health checks with records
	- Up to 8 healthy records are returned for each MultiValue query 
	- Multi Value is not a substitute for having an ELB
