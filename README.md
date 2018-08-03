## We need total three servers.
#### 1. WebServer
#### 2. AppServer
#### 3. DBServer

## 1. Install Web Server

```
# yum install httpd -y
# systemctl enable httpd
# systemctl start httpd
```

Open this server over browser wiht external ip address.

## 2. Install App Server.

```
# cd /root
# wget http://www-us.apache.org/dist/tomcat/tomcat-9/v9.0.10/bin/apache-tomcat-9.0.10.tar.gz
# tar -xf apache-tomcat-9.0.10.tar.gz
# cd apache-tomcat-9.0.10/webapps
# wget https://github.com/citb30/project-1/raw/master/student.war
# cd /root/apache-tomcat-9.0.10/bin
# sh startup.sh
# tail -f /root/apache-tomcat-9.0.10/logs/catalina.out
```
