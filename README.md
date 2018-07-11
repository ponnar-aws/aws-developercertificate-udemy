# aws-week1

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
```
#### add http 8080 port in security group
add any tcp -> add 8080 in that

### How to attach the volume to linux and make use as file system
```
lsblk
mkfs.ext4 /dev/xvdf
mkdir /app
mount -t ext4 /dev/xvdf /app
df -h
```

### how to move region to region
shapshot is created which can be region to region
(we can create script to backup regularly from 1 region to other region)

