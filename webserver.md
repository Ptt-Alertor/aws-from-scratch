## Set up EC2

### Create Key Pairs

* Enter `Key Pairs` Page
* Create Key Pairs
* Save pem file in local ~/.ssh/
* [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)

### Launch EC2

1. Enter `Instances` Page
1. Launch Instance
    1. Check `Free Tier Only`
    1. Choose Amazon Linux AMI (HVM), SSD Volume Type
1. Choose t2.micro
1. Set Network
    1. Choose default VPC
    1. Create Subnet
    1. Enable `Auto-assign Public IP`
1. Create IAM Role
    1. AWS Service
    1. EC2
1. Add Volumn
    * 22 GB of EBS General Purpose (SSD) for free tier
1. Add Tag
    * Key: Project
    * Name: your-project-name
1. Configure Security Group
    * SSH for anywhere
1. Assign Key Pairs
    * Choose we created in the previous step

### Connect by SSH

* Go to Instances Page
* Click `Connect` to show How to
* Example

    ```
    ssh -i "my-key-pair.pem" ec2-user@ec2-35-100-200-200.us-west-2.compute.amazonaws.com
    ```

    ### Install Web Server and PHP

1. Update Package to newest

    ```
    sudo yum update -y
    ```

1. Uninstall default Apache HTTP Server

    ```console
    sudo yum remove httpd*
    ```

1. Find the newest version on yum repository

    ```console
    sudo yum list http*
    ```

1. Install newest version, which find on list
    * Apache HTTP Server

        ```console
        sudo yum install httpd[version]
        ```

1. Start Web Server

    ```
    sudo service httpd start
    ```

1. Testing

    ```
    http://<your-ec2-public-dns>.<your-ec2-region>.compute.amazonaws.com
    ```

1. If connection refuse or timeout, you have to open HTTP 80 port on `Security Group`
    1. Choose instance
    1. Find `Security Groups` in `Description` tab
    1. Edit `Inbound`
    1. Add Rule: Choose Type `HTTP`, source `anywhere`
    1. Try again

### Install PHP

1. Uninstall default Apache HTTP Server and PHP

    ```
    sudo yum remove php*
    ```

1. Find the newest version on yum repository

    ```
    sudo yum list php*
    ```

1. PHP basic, includes php-cli php-common php-json php-process php-xml

    ```console
    sudo yum install php[version]
    ```

1. PHP commonly used packages

    ```console
    sudo yum install php[version]-gd php[version]-imap php[version]-mbstring php[version]-mysqlnd php[version]-opcache php[version]-pdo php[version]-pecl-apcu
    ```

1. Testing

    ```console
    php -v
    ```

### Connect Apache HTTP Server and PHP

1. Reload Apache HTTP Server to load /etc/httpd/conf.d/php.conf

    ```console
    sudo service httpd reload
    ```

1. Add `index.php` in http server default document root `/var/www/html/`

    ```php
    echo "<?php phpinfo();" > /var/www/html/index.php
    ```

1. Testing

    ```
    http://<your-ec2-public-dns>.<your-ec2-region>.compute.amazonaws.com
    ```

### Do the Best Practice

#### Set RunLevel

1. Configure Apache HTTP Server to start after system boot

    ```
    sudo chkconfig httpd on
    ```

#### Set Owner and Permission

1. Allow ec2-user could modify /var/www/

    ```
    sudo groupadd www
    sudo usermod -a -G www ec2-user
    ```

1. Re-login for refreshing group

    ```
    exit
    groups
    ```

1. Change directories' and files' owner and permission

    ```
    sudo chown -R root:www /var/www
    sudo chmod 2775 /var/www
    find /var/www -type d -exec sudo chmod 2775 {} +
    find /var/www -type f -exec sudo chmod 0664 {} +
    ```

1. Check configuration

    ```
    ll /var/www
    ```

#### Separate Project from Default

1. Create Project folder

    ```
    sudo mkdir /var/www/<project>
    ```

1. Move project content to project folder

    ```
    mv /var/www/html/* /var/www/<project>
    ```

1. Create log folder

    ```
    sudo mkdir /var/log/httpd/<project>
    ```

1. Create `/etc/httpd/conf.d/vhost.conf`

    ```xml
    <VirtualHost *:80>
        ServerName <your-domain-name>
        ServerAlias <project>
        DocumentRoot /var/www/<project>
        ErrorLog /var/log/httpd/<project>/error.log
        CustomLog /var/log/httpd/<project>/access.log combined
    </VirtualHost>
    ```

1. Reload Config

    ```
    sudo service httpd reload
    ```

1. Testing

    ```
    http://<your-ec2-public-dns>.<your-ec2-region>.compute.amazonaws.com
    ```

### User Data for Launch Configuration

user data help execution all the settings when EC2 launch

1. add s3 bucket and upload vhost.conf in bucket

1. Add s3 get object policy on EC2 IAM role

1. Migrate all Action in one bash

    ```bash
    #!/bin/bash

    yum update -y
    yum remove httpd* -y
    yum install httpd[version] -y
    yum remove php* -y
    yum install php[version] -y
    yum install php[version]-gd php[version]-imap php[version]-mbstring php[version]-mysqlnd php[version]-opcache php[version]-pdo php[version]-pecl-apcu -y
    mkdir /var/www/example.com
    mkdir /var/log/httpd/example.com
    groupadd www
    usermod -a -G www ec2-user
    chown -R root:www /var/www
    chmod 2775 /var/www
    find /var/www -type d -exec sudo chmod 2775 {} +
    find /var/www -type f -exec sudo chmod 0664 {} +
    aws s3 cp s3://<bucketname>/vhost.conf /etc/httpd/conf.d/vhost.conf
    service httpd start
    chkconfig httpd on
    ```

1. Change User data
    1. Stop Instance
    1. Instances Setting
    1. View/Change User Data
    1. Paste bash above

1. Execution log is in `/var/log/cloud-init-output.log`

### Create Elastic Load Balancer

1. Enter Load Balancer Page
1. Create Load Balancer
    1. Choose Classic Load Balancer
    1. Select All Subnets
    1. Create a security group only allow HTTP
    1. Change Health Ping Path to `/`
    1. Select Instance that we created previously
1. Testing

    ```
    curl http://[dns-name].us-west-2.elb.amazonaws.com
    ```

### Create Auto Scaling

#### Create Launch Configuration

1. Entry `Launch Configuration`
1. Create launch  configuration
1. Choose everything like EC2
1. Add `user data` we create above

#### Create Auto Scaling Group

1. Keep Group size 1 instance
1. Choose VPC and Select Public Subnets
1. In `Advanced Details`
    * check `Load Balancing`
    * fill in Classic Load Balancer
1. Auto Scaling Group
    *  Keep this group at its initial size
1. Add Notification {{{{{{uuulllllkkuu}}}}}}
1. Review and Createkkia
1. Test
    * Terminate EC2 and it will create new instance automatically
    * Connect into instance and check everything is OK
    * Terminate EC2 which we create mkanually

### Additional

* [Tutorial: Create a Web Server and an Amazon RDS Database](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/TUT_WebAppWithRDS.html)
* [Linux AMI Virtualization Types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/virtualization_types.html)
* [Best Practices for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-best-practices.html)

### Reference

* [How to install PHP 7 on EC2 t2.micro Instance running Amazon Linux Distro
](https://stackoverflow.com/a/40581436/48101990)
* []()