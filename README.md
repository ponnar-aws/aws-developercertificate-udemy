# aws-week1

### Install java and tomcat in EC2
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

