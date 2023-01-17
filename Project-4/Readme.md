## Vprofile Project 04
# Re-Architecting Web App on AWS Cloud[PASS & SAAS]

[Project Source](https://github.com/devopshydclub/vprofile-project/tree/aws-LiftAndShift)

Prerequisites:
* AWS Account 
* Maven
* JDK8
* AWS CLI

Project architecture;
![Project Architecture](IMG/1_SK7JOOc5DZZW4cDnZXZ4GA.webp)

### STEP 1
Create a `keypair`
![mailtrap](IMG/step%201.png)

### STEP 2
Create a `security group` for the backend services with the following inbound rules shown below;
![mailtrap](IMG/Step%202.png)


### STEP 3
Create the MySQL server using `Amazon RDS`. Kindly follow the steps below;
* Create a subnet group for the amazon RDS as shown below;
![mailtrap](IMG/Step%203.png)

* Create a parameter group for the RDS;
![mailtrap](IMG/step%2017.4.png)

* Then, create the RDS with the following parameters below;
  1. Select Mysql as shown in the image below; amazon aurora is also a good option, but for this project, we'll be going with mysql
  ![mailtrap](IMG/Step%205.1.png)
  2. Select MySQL v5 as this is recommended for the java application for this project while selecting the Dev/Test template.
  ![mailtrap](IMG/step%205.2.png)
  3. Select the Multi-AZ, your instance name should be the DB-instance identifier then create the database credentials. Kindly save this credentials as this would be needed in the subsequent steps.
  ![mailtrap](IMG/step%205.3.png)
  4. Select a burstable class db with `db.t3.micro`
  ![mailtrap](IMG/step%205.4.png)
  5. Select the `default vpc` and the `subnet group` created earlier
   ![mailtrap](IMG/step%205.5.png)
  6. Select the backend security group created in the previous `step 2`
   ![mailtrap](IMG/step%205.6.png) 
  7. Enable the enhanced monitoring and create a database account. As shown below, a database with the name `account` was created.
   ![mailtrap](img/step%205.7.png)
  8. Enable automated backups.
   ![mailtrap](IMG/step%205.9.png)
  9. Enable the preferred log types to be exported to cloudwatch for continous monitoring
   ![mailtrap](IMG/step%205.10.png)
  10. Create your RDS instance. NOTE: Copy your credentials.
   
### STEP 4
Create the memcached instance using Amazon ElastiCache for memcached clusters. Kindly follow the steps below;
* Create a parameter group for the Elasti Cache
  ![mailtrap](IMG/step%207.png)
* Create a subnet group for the Elasti-Cache
 ![mailtrap](IMG/step%208.png)
* Select memcache clusters to create the memcache instance, select the cluster to be created on AWS, then give the cluster name as shown below;
   ![mailtrap](IMG/step%209.png)
* Select the version, port, parameter group and the node as shown below for this project.
   ![mailtrap](IMG/step%209.1.png)
* Select the subnet group that was created in the previous step.
   ![mailtrap](IMG/step%209.2.png)
* Select the backend secutity group created in step 2
  ![mailtrap](IMG/step%209.3.png)
* Then Create the memcached instance.
 ![mailtrap](IMG/step%209.4.png)

### STEP 5
Create the `rabbit mq` with `Amazon MQ`. Kindly follow the steps below;
* Select the rabbit mq broker.
  ![mailtrap](IMG/step%2010.png)
* Select the following configuration for the rabbit mq instance.
  ![mailtrap](IMG/step%2010.1.png)
  ![mailtrap](IMG/step%2010.2.png)

### STEP 6
Initialize the RDS database created in step 3.
* Allow port `3306` in the backend security group created in `step 2` for access to the MySQL database
   ![mailtrap](IMG/step%2012.png)
* Create an ec2 instance to for the initialization, and Install mysql client.
  ```sh
      #!/bin/bash 
      sudo apt update
      sudo apt instal mysql-client -y
  ```
*  Login to MySQL and verify the `account` database created using the RDS endpoint.
   ![mailtrap](IMG/step%2013.png)
* Clone the source code of the application, change the branch to `aws-Refactor`, then cd to the `/src/main/resources/` then initilize the database as seen below;
  ![mailtrap](IMG/step%2014.png)
* Login to the `account` database to verify the initialization.
  ![mailtrap](IMG/step%2015.png)

### STEP 7
Copy the database end points which would be needed for the Elastic Beanstalk.
* RDS endpoint
  ![mailtrap](IMG/step%2016.1.png)
* RabbitMQ endpoint
  ![mailtrap](IMG/step%2016.png)
* Elasticache endpoint
  ![mailtrap](IMG/step%2016.2.png)

### STEP 8
Create the application in Elastic Beanstalk.
* Create application, select managed platform and the tomcat service as the java application would be running on tomcat.
     ![mailtrap](IMG/step%2017.png)
* Select the `configure more option`, select the security group created in step 2.
    ![mailtrap](IMG/step%2017.1.png)
* Modify the capacity with the credentials below;
    ![mailtrap](IMG/step%2017.2.png)
* Modify the rolling updates
    ![mailtrap](IMG/step%2017.3.png)
* Modify the security
    ![mailtrap](IMG/step%2017.4.png)
* Create the application. This would take a few minutes before the application and its environment is `ok`.
    ![mailtrap](IMG/healtcheck%20loading.png)
    ![mailtrap](IMG/step%2017.5.png)


### STEP 9
Add additional rules to the backend security group to allow access from the Elastic Beanstalk Security Group. A security group would be created automatically for the Elastic Beanstalk.
  ![mailtrap](IMG/step%2018.png)

### STEP 10
Validate the application with the Elastic Beanstalk endpoint.
![mailtrap](IMG/final%20.png)
