# openfire-monit-java8
install openfire as service &amp; monitor performance through monit 

Operating System : Centos 6.5


#download jdk 8
wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u66-b17/jdk-8u66-linux-x64.tar.gz"

tar xzf jdk-8u66-linux-x64.tar.gz

#install java with alternatives
cd /opt/jdk1.8.0_66/
alternatives --install /usr/bin/java java /opt/jdk1.8.0_66/bin/java 2
alternatives --config java
alternatives --install /usr/bin/jar jar /opt/jdk1.8.0_66/bin/jar 2
alternatives --install /usr/bin/javac javac /opt/jdk1.8.0_66/bin/javac 2
alternatives --set jar /opt/jdk1.8.0_66/bin/jar
alternatives --set javac /opt/jdk1.8.0_66/bin/javac

# add globale path -- comented for time being as we will set parmenant variable
# export JAVA_HOME=/opt/jdk1.8.0_66
# export JRE_HOME=/opt/jdk1.8.0_66/jre
# export PATH=$PATH:/opt/jdk1.8.0_66/bin:/opt/jdk1.8.0_66/jre/bin


#create a file called java.sh under /etc/profile.d/ directory.
vi /etc/profile.d/java.sh
#add below text & save
#!/bin/bash
JAVA_HOME=/opt/jdk1.8.0_66
JRE_HOME=/opt/jdk1.8.0_66/jre
PATH=$PATH:/opt/jdk1.8.0_66/bin:/opt/jdk1.8.0_66/jre/bin
export PATH JAVA_HOME JRE_HOME
export CLASSPATH=.

#make it executable
chmod +x /etc/profile.d/java.sh

#set the environment variables permanently
source /etc/profile.d/java.sh

#download openfire
wget http://download.igniterealtime.org/openfire/openfire-3.10.2-1.i386.rpm
yum install -y /root/openfire-3.10.2-1.i386.rpm

#check dependencies
yum install -y glibc.i686

# start on reboot
chkconfig openfire on

#run
service openfire start

#now install monit 
yum install monit y

#edit config file
vi /etc/monit.conf

#make an alert rule file
vi /etc/monit.d/openfire 

check process openfire
	with pidfile "/var/run/openfire.pid"
	start program = "/sbin/service openfire start"
	stop program = "/sbin/service openfire stop"
	#if 5 restarts within 5 cycles then timeout
	#if cpu usage > 95% for 11 cycles then restart
	#if totalmemory > 512 MB then restart # This can be any number...
	#if failed port 5222 then restart
	#if failed port 5223 type tcpssl then restart
	#if failed port 5229 then restart
	#if failed port 5269 then restart
	#if failed port 7070 protocol http then restart
	#if failed port 7443 type tcpssl protocol http then restart
	#if failed port 7777 then restart
	#if failed port 9090 protocol http then restart
	#if failed port 9091 type tcpssl protocol http then restart
  alert youremailid@gmail.com

#increase jvm settings based on memory of machine
vi /etc/sysconfig/openfire


#add cluster to all existing chat servers 
vi openfire/plugins/hazelcast/classes/hazelcast-cache-config.xml 
<member>your private ip</member>

#import ssl certificates from web consol or make it self signed.. 

#check on the browser
http://yourpublicip:9090


