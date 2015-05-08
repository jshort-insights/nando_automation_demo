# nando_automation_demo

All-In-One Amazon AWS automated demo from a single cli command:

```
==> ./deploy.nando.automation.demo.sh [TrustedIP1] [TrustedIP2] [TrustedIPX]
```
![demo architecture](http://nando-automation-demo.s3.amazonaws.com/public/nando-automation-demo-001.png)
![demo architecture](http://nando-automation-demo.s3.amazonaws.com/public/nando-automation-demo-002.png)

- Cloud Deployment with [CloudFormation](http://aws.amazon.com/cloudformation/).

- Configuration Management with [Puppet](https://github.com/puppetlabs/puppet).

- Continuous Delivery with [Jenkins](https://jenkins-ci.org/), [CodeDeploy](http://aws.amazon.com/codedeploy/), and [Docker](https://www.docker.com/).

- High Availability with [ELB](http://aws.amazon.com/elasticloadbalancing/) and [ASG](https://aws.amazon.com/autoscaling/).

- [S3](http://aws.amazon.com/s3/) for static templates, manifests, encrypted keys, and demo outputs.

- [RDS](http://aws.amazon.com/rds/) for storing image tags and paths.

- [IAM](http://aws.amazon.com/iam/) and [S3](http://aws.amazon.com/s3/) roles and policies for security.

- [ElasticBeanstalk](http://aws.amazon.com/elasticbeanstalk/) for [Docker](https://www.docker.com/) containers. 

- [ElastiCache](http://aws.amazon.com/elasticache/) for user session data.

- Testing via [ChaosMonkey](https://github.com/Netflix/SimianArmy/wiki/Chaos-Monkey), [ServerSpec](http://serverspec.org/), [TestKitchen](https://github.com/test-kitchen/test-kitchen), [Cucumber](https://cukes.info/), [OWASP ZAP](https://code.google.com/p/zaproxy/), [CloudCheckr](http://cloudcheckr.com/), [evident.io](https://evident.io/), and [Trusted Advisor](https://aws.amazon.com/premiumsupport/trustedadvisor/).

- [Vagrant](https://docs.vagrantup.com/v2/) for Developer Environments.


This demo creates ElasticCloud and VirtualPrivateCloud infrastructure and launches inside with one command. One pipeline control box with Puppet and Jenkins, a CodeDeploy Nginx/PHP-FPM Application via AutoScalingGroup, a Docker container via ElasticBeanstalk, private subnet Multi-AZ HA RDS databases , and ElastiCache for temporary user session data. ElasticLoadBalancers, resolved by Route53 RecordSets, sit in front of both CodeDeploy and Docker web tiers. SimpleStorageService is used for authenticated retrieval of static templates, manifests, and encrypted keys needed upon resource bootstrap. S3 is also used for the running demo to store logs and other outputs securely. 

homepage click thru video:

[![Demo CountPages alpha](http://share.gifyoutube.com/KzB6Gb.gif)](https://www.youtube.com/watch?v=ek1j272iAmc)

http://nando-automation-demo.elasticoperations.com displays two random Instagram images. The end-user selects their prefered image, and then proceeds to click thru a series of images pairs (CodeDeploy). Based on the user's selections, an Instagram image slideshow is generated and displayed (Docker).  S3 stores the images, RDS stores the path and tags, and ElastiCache stores the end-user's session data. Jenkins continually delivers the CodeDeploy application, as well as the Docker container, thru all stages of the Continuous Delivey Pipeline. 

Jenkins jobs running Video:

[![Demo CountPages alpha](http://share.gifyoutube.com/KzB6Gb.gif)](https://www.youtube.com/watch?v=ek1j272iAmc)

Acceptance tests ensure the all resources are up and working correctly, and that the application and environment are secure.



# Pipeline Blueprint (COMPLETE)

- deploy-pipeline-1-command (Bash script launches all jobs and resources)
- provision-environment (CloudFormation)
- node-configuration (Packages, Files, Services)
- poll-version-control (GitHub every minute https://github.com/stelligent/nando_automation_demo)
- app-deployment: (CodeDeploy and Docker)
- configure-local-environment-1-command: (Vagrant and Docker) 



# Pipeline Blueprint (PENDING):

- node-configuration: (data encryption, security hardening, test db and local tests)
- run-application-build: (Rake, Maven, Ant, Grunt)
- store-distros: (Nexus, Artifactory, S3)
- run-unit-tests: (RSpec, JUnit, XUnit)
- run-static-analysis: (CheckStyle, PMD, Sonar, CodeClimate, JSONLint, ValidateTemplate, ratproxy, Foodcritic)
- run-infrastructure-tests: (ServerSpec, Cucumber)



# Pipeline Security:

- Jenkins IAM roles (COMPLETE)
- Ensure latest code on deploy (COMPLETE)
- Layer 4: tcp/ip only from known admin ip/subnets (COMPLETE)
- Layer 7: jenkins application security (COMPLETE)
- Trusted Advisor (PENDING)
- Jenkins Iptables Firewall (PENDING)



# Application Security:

- Private VPC subnet (COMPLETE)
- Application Pen Testing https://github.com/OWASP (PENDING)
- Manual Curl Testing for HTTP responses (PENDING)
- Instance Intrusion Detection https://github.com/ossec/ossec-hids (PENDING)
- Remote Logging (https://github.com/Graylog2 or S3) (PENDING)
- Instance Iptables Firewall (PENDING)



# process_notes

- uploads jenkins templates to S3
- uploads puppet manifests to S3
- creates keypair for use in demo
- launches cfn stack
	- builds vpc and dependancies
	- builds Webserver ASG and ELB with Route53 Alias
	- sets up S3 bucket for all logging and demo output
		- buckey policy restricts access to trusted IPs
		- website indexing is enabled
	- builds Multi-AZ MySQL RDS for storing image tags and paths
	- launches ElasticBeanstalk for Docker
	- bootstraps jenkins server via cloud init and authenticated S3
		- adds python instagram functionality
		- adds git functionality
		- installs and configures jenkins via puppet
		- pulls jenkins job templates from authenticated S3 and creates jenkins jobs
		- jenkins executes based on SCM
			- gets instagram images and generates html
			- pushes code and images to staging (pending)
			- application and security acceptance testing (pending)
			- push to production with CodeDeploy
- vagrant for developer environments
	- launches docker and codedeploy web tiers
	 	- codedeploy php stack mapped to tcp/8010
	 	- docker python stack mapped to tcp/8011
	- launches jenkins with minimal jobs
		- jenkins gui mapped to tcp/8888
	- launches mysql 5.6 (RDS version)
		- mapped to default tcp/3306




# todo

- push private key for jenkins from cfn paramater to s3 kms
- change sleep in cfn-init for instance bootstrap to AWS::CloudFormation::WaitCondition
- rewrite deploy script in python
- ChaosMonkey, ServerSpec, TestKitchen, Cucumber, OWASP ZAP
- ElastiCache for user session data

