# aws-sessions

## security IAM  - identity access management
1. policy eg: s3fullaccess, admin access,
2. user - attach user with policy
3. roles - attach policy with roles. eg. s3 access which makes ec2 to communicate with s3
4. group - attach policy with group. group has many users in it. these policies embed to the user policies
5. security key - cli/program access to ec2/any resource or ec2 can communicate with s3/other resource
#### Lab
- IAM is global.
- In dashboard, standard security status is to be checked. 1. delete ur root access key 2. activate MFA 3. create individual user 4. use groups 5. apply IAM password policy
- default user created doesnt have any access(No permission) or policy attached to it.
- access id and secret  key(availbale only once).

## EC2 Elastic compute cloud and EBS elastic block storage
EC2 is simply a virtual machine with different RAM config depends on the usage. we can enter into the instance using SSH or programatically cli or any program.
- default have 8 GB storage ssd. we can select 5 different storage disk.
- we can create Elastic block storage(EBS) and attach to the EC2
- create security group (inbound and outbound protocols)
- use ssh to connect. dwn .pem file to connect  into the ec2. for windows, create ppk file from .pem file and connect using putty.
Topics not covered in EC2:::  auto scaling in ec2. 
```
connect to ec2-user@<ip of ec2> using putty
> sudo su // to root user
> yum update y  // update security if any
> yum httpd install
> service httpd start
> chkconfig httpd on // if reeboot, make httpd start automatically
> service httpd status  // start the service
> cd /var/www/html   // apache folder 
> nano index.html // create a web page

open the chrome and check the ec2 ip or public url to see the page
```
### create EBS volume and attach to the EC2
create volume of desired size // Note ec2 and volume to be same AZ to attach. if volume and EC2 in different location, cant attach the volume
once created, we see available status. right click-> attach volume to the ec2 instance. once attached, ssh to instance and command lsblk. we see new volume displayed.

### How to attach the EBS volume to linux and make use as file system
```
lsblk // list the default disk along with attached disk. 
mkfs.ext4 /dev/xvdf  // we are formating the new volume to filesystem. Note that if take image of EBS it is already a file system so formating that will delete all the data in it.
mkdir /app
mount -t ext4 /dev/xvdf /app
df -h
```

## Route53 And Elastic Load balancer ELB
- types: application LB, Network LB, Classic LB
- x-forwarded-For header provides the public ip of the end user to the ec2 which runs behind the LB.
- Route53 is DNS service. Global. create a domain or use already owned domain and map to your EC2 or ELB or S3. domains are not free.
DNS is Ip and url mapping servers. it is communicate with all DNS servers in the world.
- Route53- hosted zones where create a map bw domain with the EC2/ELB/S3. Different Types(A record(ipv4),AAAA(ipv6 mapping),etc ). map is created by choosing target in alias.
```
click the load balancer in ec2 page left tab itself(click appliaction ELB or nw ELB, classic ELB)
configure listner(http/https)
select AZ(all selected in demo)
select security group(http/https)
configure routing targets(target to ec2 or s3 alone etc) // add existing EC2 to this group ie mapping of ec2 to ELB
if we have r53 and domain ready. we can view through domain now. or click the public elastic url and check it is working.
```

## Install java and tomcat in EC2
```
yum list installed
yum install java-1.8.0
yum remove java-1.7.0-openjdk
yum list installed|grep java
java -version
yum install wget // for dwn from web
wget http://apache.mirrors.lucidnetworks.net/tomcat/tomcat-8/v8.5.32/bin/apache-tomcat-8.5.32.tar.gz
mkdir software
mv apache-tomcat-8.5.32.tar.gz /software
cd software
tar xvfz apache-tomcat-8.5.32.tar.gz
./bin/startup.sh  // starting tomcat
ps -ef | grep tomcat
wget http://localhost:8080 // it gives default index page
./bin/shutdown.sh
```

```
To manage tomcat admin console:
add the entry at the end to conf/tomcat-user.xml
<role rolename="manager-gui"/>
 <user username="admin" password="admin" roles="manager-gui"/>
 Now you can browse the tomcat admin page with these user/password
```
In real world, we need to create a less priviledged user and run tomcat with that for security reasons
```
todo
```
#### add http 8080 port in security group
add any tcp -> add 8080 in that

### how to move region to region
shapshot is created which can be region to region
(we can create script to backup regularly from 1 region to other region)

# week2
### using CLI 
you can do everything in cli as well like in web console
```
>aws s3 ls // this gives error for configure secret key
>aws configure // it asks for access id  & key
go to aws console and create a user with access. create a secret id and key
this is secret key: 3gBA1jJkcG97pN6t2SwkDURe4XU+HR3/FOcNkKf/ get the id AK***7LA from console.
>aws s3 ls  // shows nothing ie there is nothing in s3 storage
>aws s3 mb s3://poppy-2018jul   // make bucket you can check in console as well
> aws s3 cp hello.txt s3://poppy-2018jul   // create a text file and copy paste that into s3. we can see this in web also. make pulic to the resource before we see via web
https://docs.aws.amazon.com/cli/latest/index.html // all the CLI commands

```
### EC2 with S3 bucket
S3 and EC2 are different resources in AWS. 
2 ways to communicate:
1. accesskey created for user can access S3.
2. create role(IAM role)(can create for EC2,lamda etc) and attach to EC2 so that EC2 can access.
```
mysecuritycredential-> create a role for resource EC2 -> attach policy(like s3 fullaccess)
in ec2 instance -> actions->instance setting -> attach IAM role 
>cd ~/.aws  // this stores the secret key values in that. remove that to make use of IAM role. For windows, >dir "%UserProfile%\.aws"
>ls
> rm credentials config 
>aws s3 ls // now we able to see the s3 data without secret key
> aws s3 cp hello.txt s3://<s3 url>
> aws s3 ls s3://<s3 url>
```
exam Tips: roles are preferred over secret key for security. immediate effect applied when role is added to running instance as well.

### RDS relational database service
it is also linux box with DB servers installed in default. currently supported DB are MS sql server, oracle, mysql,postgre, amazon aurora, mariaDB
basics :non relational db - collection(table), document(rows),key value pair json(fields)
basics :data warehousing - business intelligence - analytical processing. large queries. 
basic term : elastic cache - in-memory cache(for some frequent data) instead of relying on slower disk-based DB.supports- memcached/redis
```
create rds in web console by providing dbinstance name, db name(connection name is already available). we selected mysql version of rds.
create new ec2 instance to run script(advanced tab while creating ). below script
#!/bin/bash
yum install httpd php php-mysql -y
chkconfig httpd on
service httpd start 
echo "<?php phpinfo();>" > /var/www/html/index.php
cd /var/www/html
wget s3://<../connect.php>
```
Note: this auto script is not woring for me. And php is also couldnt complete.
I needed to restart the instance to see php installed.
#### connectin rds from ec2
1.EC2 :internet - VPC- subnet -security group(firewall)- user(ec2-user)/roles/policy[ec2] secret key is stored privately.can be public.
2. RDS: internet - VPC- subnet -security group(firewall)- user(DB user created during creation)has roles/policy.can make public available.
3. S3: internet - VPC- subnet -security group(firewall)- no user created for this s3 linux machine. so, user with security key and s3 access role can access. or create role  and attach ec2 so that it can access.[s3] in s3 we can restrict who to access bucket rule.


1 modify the security group of RDS. add inbound and modify to add security group of ec2. Connection to establish bw one security group to other security group--not working
doubt::: in ec2 we add iam role(s3fullaccess) so that we connected to s3. In S3 also we can restrict who can access. how rds didnt follow such step.
2 we can use SSH tunnelling. EC2 is publically accessable. From workbench, Standard TCP/IP over SSH option. give EC2 detail and my sql detail. idea is from EC2 we can connect mysql. may be both mysql and ec2 in same subnet group.
3 we can set as public while creating rds. then connect using workbench. vpc -rdslaunch wizaard is used in youtube video instead of default.--working fro home.
4 in ec2. > mysql -h host -u user -p pass....guess need to install mysql client in it

### RDP Multi AZ & Read replica -- must questions in this segment
2 Types of backups: 
1. Automated Backup - enabled default, take daily snapshot with transaction logs for retention period(0-35 days).
if you have 10gb RDS instance,you get 10GB worth of S3 storage as well. backups are stored in that s3. backups are happening daily.
2. Database snapshots - we need to do manually the snapshots. its available even after our rds deleted.
doubt : snapshot includes all the data?? 
automatic backup contains data as well?? 

when you restore the RDS(auto or manual), new instance is created ie it has new url.
encryption is supported. enryption is done by AWS key management.
tips: For encryption, create copy of DB snapshot and enable encryption and create instance.

Multi AZ - for disastar recovery not for performance. for performance, use read replica.scale horizontal
eg: 1 instance in 1 location other in different location.
Read replica -scaling from primary rds prod - onlyread- asyncronous replication-  For performance improvement. can have upto 5 read replica and usually most of queries are read query. automatically data duplicates in read replica instance. can have read replica that have multi AZ or in different region. has own DNS endpoint. scale vertical.

### Elastic cached 
in-memory cache in cloud. improves performance of web application by retrive info from in-mem cache instead of slow disks. can store frequent info to these cache.
1. Memcached - object cache that can grow and shrink similar to ec2.
2. redis - popular open source key-value store. supports master-slave and multi AZ. can used for OLAP/ leader board.
Tip: when to use memcached or redis?
Topic not covered::: elastic cached

## S3 storage
- S3 - storage service . not region specific global so name also unique for globally.
storage of object based (key- file id and value-data). unlimited storage. spread across region for backup automatically. allows versioning.
  - data consistency : when upload file it is availble instantly. when update or delete it takes some tiem to reflect.
  
access for S3 :
S3: internet - VPC- subnet -security group(firewall)- no user created for this s3 linux machine. so, user with security key and s3 access role can access. or create role  and attach ec2 so that it can access.[s3] in s3 we can restrict who to access bucket rule.


default access is private(only owner has access). 
how others resource like ec2 or person can access S3? 
S3 and EC2 are different resources in AWS. 
2 ways to communicate:
1. accesskey created for user can access S3.
2. create role(IAM role)(can create for EC2,lamda etc) and attach to EC2 so that EC2 can access.
can create bucket policy in permission tab.
how one s3 access other resource?
1. using CORS we can share the resources
2. 

#### encryption while upload- imp for s3 exam 
if sirverside encryption mandate in upload time, 
x-amz-server-side-encryption header is used in PUT request.
2 encryption:
x-amz-server-side-encryption : AES256(s3 managed) or ams:kms(kms managed)

## S3 as static webpage hosting
we can make s3 files to host static content. publicaly available.
CORS - to access files bw 2 S3 buckets.

### CloudFront
independent of AZ/regions. there are over 100 cloudfronts available.
2 types: web and RTMB(for media)

S3 optimization:
1. use cloud front for GET intensive 
2. mixed contents - avoid sequential key names . rendam names.

topics not covered :
EFS - elastic file system - can be considered as shared location. EFS can be attached multiple EC2 at a time.
storage gateway - useful when migration.
volume gateway - migration helpful. some process in on premise other in aws.

## Serverless computing


## KMS service
- note - kms charged me 0.04$ after created. dont know the pricing in free tier.

## SQS 
oldest service of AWS. so must question in exam. it is webservice to queuing service.
->put the message in SQS->some service like ec2 polls the messages and process
- SQS is pull based not push based. default retention is 4 days. canbe 1 min to 14 days.
- invisiblity time is when picked by consumer but not processed yet. default invisiblity time is 30sec. max 12 hrs. after the timeout msg is visible again to process.
- can auto scale based on message size
- message can be 256kb of any format
- can be consumed programitically sqs API
- helps to decouple the application
- can be used as buffer if producer or consumer is slow or intermittent
- 2 types: 1. standard queue (default) limited transaction, order is not quaranteed though usually same as order of insertion.
  2.FIFO type : 300 transaction per sec. first in first out. no duplicate messages . it is available till cunsumer process and deletes the message.
  
## SNS simple notification service
- send notification from cloud to subscriber. ie to any http endpoints like mobile devices like android, apple, or sms or email or sqs or even lamda function
- one topic can congigure to push only all android , apple device etc
- push based system. publish-subscribe or pub-sub model
- pricing -> .50$ for 1 million amazon requests / .06$ for 100000 notifications over http/ .75$ for 100 sms / 2$ for 100000 email notification
- very simple API needed to implement push system in our application

## CI/CD for developers in aws
- CI/CD are best practises in software developemnt and deployment
- frequent updates can be done without affecting the service
- CI-focuses even small changes to be traced and into main repo once tested: coders ->code repository like git ->build management system(for each commits)->test framework(ensure commits didnt introduce new bugs to the code)
- CD - continuous delivery(manual intervention is needed) : merged changes are automatically built,tested and prepared to release to stage /prod
  from code repo/test framework ->deploy packaged app ->environments like stg/prod
- CD continuous deployment :as soon as new code is tested and available it is available to the release. small changes are going to prod easily very fast.
- 

## SES simple email service
- also messaging service like sns. mostly for marketing email . 
- it also able to receive incoming email and move to s3.
- no need to subscribe . mailId enough to send notification, where as for SNS topics to be subscribed.

## Elastic beanstalk
- service to deploy web applications, auto scale, load balance, app health built using java,.net,node etc. it has support for tomcat, nginx, etc. we need to configure the infrastructure depends on our requiement.
- you has underlying ec2, s3, etc required for your application.
- dont have ec2 terminal control but have all admin access to control it.
- in exams -> elastic beans configurations - rolling updates & deployment tab - important
- in exams -> EBS deployment policies important. 1. all at once(deploys new version in all instances.dwntime will be there) 2. rolling(deploys in batches. not ideal for mission sesitive applicaitons) 3. rolling with added batch policy(creates new batchs new autoscaling grp. when everything ok switches to normal. useful for mission critical applicaiton. this also requires  further rolling updates when rollback.) 4.immutable- it also mains full capacity suits for mission critical apps.to rolback, just delete new instances and autoscale grp.
- lab: we can test the above scenerios. there is option 'application version'-> we can upload the code and deploy. this deploy is actually rolling deploy. so, in configuration we can select type of deploy (all in once, immutable, rolling , rolling added batch). this lab gives downtime to the application.
- advanced topics:
- can customise elastic beanstack environment with config file to configure resource like elastic load balancer etc- file can be json or yaml and must be inside the folder .ebextensions(shd be top level directory of application) and with the file ending .config. 
- RDS with Elastic beanstalk : 2 ways - 1. within beanstalk envi 2. decouple the rds from beanstalk. advantage of having rds seperate: when ebs destroyed it destroys all resource including db rds also so decouple is best in prod. when rds is decoupled, 1. additional security grp to be created & 2. provide rds connection details to app server using beanstalk env properties. so that ec2 able to connect with rds resides outside.

## Kinesis
- Streaming data is data continuously generated and can be send to servers. eg: IOT devices which sends real data continuously, uber riding data etc
- exam - ask the scenerio and pick which type of kinesis best suits?

- 3 types: 
1.kinesis streams - 24hrs max retention is 7 days
- structure: producers(ec2/mobile/laptop)->list of shards-> consumers(ec2 usually which does some analysis, which inturn sends to db or s3 etc)
- data capacity of streams based on shards number
reads - 5 transaction/sec max of 2mb/sec
write - 1000 records/sec max of 1mb/sec(include partitionkeys)
2.kinesis firehose 
- producers -> automated(no need to mention shards automatic only or data retention(ie no retention))->s3 or elasticsearch clusters only mostly
3. kinesis analytics 
- producers -> firehose + streams(able to run sql like queries) ->s3 or redshift or elastic search clusters.
- lab: kinesis based cloudformation stack created from template given(https://s3.amazonaws.com/kinesis-demo-bucket/amazon-kinesis-data-visualization-sample/kinesis-data-vis-sample-app.template) it creates ec2 instance for producer and consumer, db to store the data, 2 shards- note shared shards cost me 0.1$ for 12 hrs
