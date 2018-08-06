# Document for Multiple Node Stack, Means (Web Server, App Server & DB Server on individual nodes)

## Step 1 : Db Server Setup. (Setup it on DB Server)

#### 1. Install DB Server
```
# yum install mariadb-server -y
```

#### 2. Start MariaDB Service
```
# systemctl enable mariadb
# systemctl start mariadb
```

#### 3. Create DB and Create users to access DB.

Create a file `student.sql`
```
# vim student.sql
```

Copy the following content into that file.
```
create database if not exists studentapp;
use studentapp;
CREATE TABLE if not exists Students(student_id INT NOT NULL AUTO_INCREMENT,
	student_name VARCHAR(100) NOT NULL,
        student_addr VARCHAR(100) NOT NULL,
	student_age VARCHAR(3) NOT NULL,
	student_qual VARCHAR(20) NOT NULL,
	student_percent VARCHAR(10) NOT NULL,
	student_year_passed VARCHAR(10) NOT NULL,
	PRIMARY KEY (student_id)
);
grant all privileges on studentapp.* to 'student'@'%' identified by 'student@1';
flush privileges;
```

After that load this file to mysql.
```
# mysql < student.sql
```

## Step 2 : App Server Setup. (Tomcat) (Setup on Application Server)

#### 1. Install Java
```
# yum install java -y
```

#### 2. Download Tomcat and extract it
```
# cd /root
# wget -O- http://www-eu.apache.org/dist/tomcat/tomcat-9/v9.0.10/bin/apache-tomcat-9.0.10.tar.gz | tar -xz
```

#### 3. Configure Tomcat

Download war and jar files
```
# cd /root/apache-tomcat-9.0.10
# rm -rf webapps/*
# wget https://github.com/cit-aliqui/APP-STACK/raw/master/student.war -O webapps/student.war

# wget https://github.com/cit-aliqui/APP-STACK/raw/master/mysql-connector-java-5.1.40.jar -O lib/mysql-connector-java-5.1.40.jar
```

Configure `context.xml` for DB connection.

Open `conf/context.xml` and add the following content just before the last line.

```
<Resource name="jdbc/TestDB" auth="Container" type="javax.sql.DataSource"
               maxActive="50" maxIdle="30" maxWait="10000"
               username="student" password="student@1"
               driverClassName="com.mysql.jdbc.Driver"
               url="jdbc:mysql://10.142.0.7:3306/studentapp"/>

```

Note: IP `10.142.0.7` is DB server IP address, If you are using same host then give the IP address of that host, but not `localhost`

#### 4. Start your tomcat
```
# /root/apache-tomcat-9.0.10/bin/startup.sh
```

## Step 3 : Web Server Setup (HTTPD) (Set on Web Server)

#### 1. Install web server
```
# yum install httpd httpd-devel gcc -y
```

#### 2. Download ModJK and comnpile it
```
# cd /root
# wget -O- http://www-us.apache.org/dist/tomcat/tomcat-connectors/jk/tomcat-connectors-1.2.43-src.tar.gz | tar -xz
# cd /root/tomcat-connectors-1.2.43-src/native
# ./configure --with-apxs=/usr/bin/apxs
# make 
# make install
```

#### 3. Create ModJK config files

Create mod-jk.conf
```
# cat /etc/httpd/conf.d/mod-jk.conf
LoadModule jk_module modules/mod_jk.so

JkWorkersFile conf.d/worker.properties
JkMount /student local
JkMount /student/* local

```

Create worker.properties file
```
# cat /etc/httpd/conf.d/worker.properties 
worker.list=local
worker.local.host=localhost
worker.local.port=8009
```

#### 4. Restart Web Server.

```
# systemctl enable httpd
# systemctl start httpd
```



