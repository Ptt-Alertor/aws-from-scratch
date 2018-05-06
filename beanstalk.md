# Set up by Beanstalk

Beanstalk is easy to deploy a simple app, but hard to integrate with other services and customize settings.

## Basic

1. Get into [AWS Beanstalk](https://aws.amazon.com/tw/elasticbeanstalk/)
1. Get Started
1. Naming the sample application, choose `PHP` as our platform and start with `Sample Application`
1. **Click Configuration More Options**
1. Try choose `Custom Configuration` and click modify on each panel
1. Read every field in each panel, try to understand the meaning, and how to control in [free tier](aws.amazon.com/free)
1. Choose back to `Low Cost` and click `Create app`
1. Waiting for creating to finish, and access sample web page by `subdomain.region.elasticbeanstalk.com.`
1. Follow [AWS Quick Start](https://docs.aws.amazon.com/quickstarts/latest/webapp/welcome.html) to try and understand more.

## Upgrade Instance

* In beanstalk Configuration Page, Choose Instances to modify
* Upgrade EC2 instance to t2.micro
  * free tier
    > 750 hours per month of Linux, RHEL, or SLES t2.micro instance usage
* Set [EBS (Amazon Elastic Block Storage)](https://aws.amazon.com/ebs/) for your instance
  * free tier
    > 30 GB of Amazon Elastic Block Storage in any combination of General Purpose (SSD) or Magnetic, plus 2 million I/Os (with EBS Magnetic) and 1 GB of snapshot storage*
* Try to connect ec2 instance by SSH
  * Set EC2 key pair
    * [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)
  * Set EC2 Key Pair in Beanstalk Configuration `security` section
  * Check port 22 open for public in EC2 security group and Network ACL

## Auto Scaling

To add a Load Balancer, we have to enable load balancing

* In beanstalk Configuration Page, Choose Capacity to modify
* Change Type to `Load Balanced`, Follow step in docs: [Environment Types](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features-managing-env-types.html#using-features.managing.changetype)
* Setting for free tier
  * Instances max:1, min:1
  * Scaling Trigger aims to keep at least 1 healthy host

## Add Load Balancer

* In beanstalk Configuration Page, Choose Load balancer to modify
* Create a Classic Load Balancer for free tier
  * [Configuring a Classic Load Balancer](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environments-cfg-clb.html)
  * Test Access Web Page by ELB endpoint

## RDS (SQL Database)

**We don't want use Beanstalk integrated database because it's not for production use**
─ [Adding a Database to Your Elastic Beanstalk Environment](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.managing.db.html)

* Choose RDS Service
* Create RDS SQL database, choose one Database Engine you like
  > * 750 Hours per month of db.t2.micro database usage (applicable DB engines)
  > * 20 GB of General Purpose (SSD) database storage
  > * 20 GB of storage for database backups and DB Snapshots
* Check `Only enable options eligible for RDS Free Usage Tier`
* Set everything
* Try to connect database from ec2
* Write a simple app that could get query result from database and show on web page
