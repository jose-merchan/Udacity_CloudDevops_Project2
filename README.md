# Udacity_CloudDevops_Project2
The configuration contained in this repository respondes to the architectured depicted in this diagram:

https://lucid.app/lucidchart/7b492e3a-e27b-48ee-82ae-e3586c9c2758/edit?invitationId=inv_27cff314-ee1d-4b4d-b2c3-0afb6d7b2253

An Aplication load balancer for port 80 is deployed into two public subnets part of the same VPC. A number of web servers are deployed in an ASG by means of a launch configuration.
Just so I play around a bit with parameter I set some default parameters for AMI and Instance Type that are overriden by the parameter configuration passed when running "aws cloudformation" commands.

The network topology is the same used during the different challenges of this section where we have:
* A single VPC with an Internet Gateway
* Two public subnets.
* Two private subnets.
* Two NAT gateways associated to each public subnet.
* For public subnet default route goes to Internet Gateway, whereas for private subnet the default route point to each of the Nat Gateways.
* Security Groups
  - Security Group for web servers that provides
    - Access to port 80 (inbound). Rule has been extended with port 22 and ICMP so we can check access from bastion server.
    - Unrestricted Outbound access
  - Security Gropu for Bastion Server:
    - Access to port 22 from HomeIP
    - ICMP from within VPC
    - Unrestricted outbound access.
  - Security Group for Load Balancing providing access on port 80 in and out

Server are deployed as ASG by means of a launch configuration:
  * ASG creates a pool of 4 servers (with mean size of 3 and max of 5)
  * That makes use of the provided User Data. 
  * Instance type and AMI are conforming the specifications.
  * IAM role to have S3 Bucket access

Load balancer listens on port 80 and forwards traffic to the WebAppTargetGroup on the same port (port 80 TCP).

An S3 bucket is created and the corresponding IAM role and permissions are created to provide EC2 instances of access to the bucket.

Finally, the Load Balancing FQDN is provided as output. Likewise, the bastion IP address is provided to facilitate the access to the platform
