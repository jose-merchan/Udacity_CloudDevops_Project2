# Udacity_CloudDevops_Project2
The configuration contained in this repository respondes to the architectured depicted in this diagram:

https://lucid.app/lucidchart/7b492e3a-e27b-48ee-82ae-e3586c9c2758/edit?invitationId=inv_27cff314-ee1d-4b4d-b2c3-0afb6d7b2253

![Project2](https://user-images.githubusercontent.com/23102562/160429619-2d33e2a9-6a08-43f1-87fe-0a6a0386f478.jpeg)


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

We can verify that the S3 permissions are ok by:
1. Ensure there is SSH access from the bastion server to the EC2 instances in the private subnet.
2. install aws cli tools and the type "aws s3 ls"
![Captura de Pantalla 2022-03-28 a las 17 04 42](https://user-images.githubusercontent.com/23102562/160428250-2b3b4cd6-64f2-484e-a375-8a7bdc58985e.png)

Likewise we can verify that the configuration is ok by clicking on the LB DNS name provided in the output
![Captura de Pantalla 2022-03-28 a las 17 06 36](https://user-images.githubusercontent.com/23102562/160428801-8e9f6ea9-b3e2-4f79-a83e-5c23718190ca.png)


A key-pair must be created so we can access the bastion server. The Keypair name must be specified on the parameters file

````json
[
	{
		"ParameterKey": "EnvironmentName",
		"ParameterValue": "Project2"
	},
    {
		"ParameterKey": "AMI",
		"ParameterValue": "ami-0e472ba40eb589f49"
	},
    {
		"ParameterKey": "InstanceType",
		"ParameterValue": "t3.medium"
	},
	{
		"ParameterKey": "KeyPair",
		"ParameterValue": "project2"
	}

]
````


To run the cloudformation script
```bash
aws cloudformation create-stack --stack-name project2 --template-body file://project2.yml --parameters file://project2.json --capabilities "CAPABILITY_IAM" "CAPABILITY_NAMED_IAM"
````
After running this code we get the following URL: proje-WebAp-1IO3TRM6HOTYN-1667141232.us-east-1.elb.amazonaws.com




