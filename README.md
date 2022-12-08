# README #

Jenkinsfile is updated with ec2-user as user name to connect with Ansible, K8s master nodes while copying the config files and running the tasks remotely. 
ing

#Testing Web Hooks ###

### What is this repository for ####

* Quick summary
* Version
* [Learn Markdown](https://bitbucket.org/tutorials/markdowndemo)

### How do I get set up? ##

* Summary of set up
* Configuration
* Dependencies
* Database configuration
* How to run tests
* Deployment instructions

### Contribution guidelines ###

* Writing tests
* Code review
* Other guidelines

### Who do I talk to? ###

* Repo owner or admin
* Other community or team contact


Procedure:-

1. Install git and maven in /opt/
2.clone repo in root /
3.go to project repo folder wher u find pom.xml
4 Execute this command "/opt/maven/bin/mvn package" where /opt/maven/bin/mvn this is the executable path of maven as we dont set it into system environment variable and package to make artifactory build from it.
 
 ## Pushing Code to Sonarqube and send Report to Database##
 1. Create Medieum ec2-instance with this setting
 ![pi](https://user-images.githubusercontent.com/106534507/206429968-f5587db8-1791-4e33-a985-df0b14c68b4a.jpg)

amazon-linux-extras install postgresql14 -y
yum install postgresql-server postgresql-devel postgresql -y

3. Intilize the postgresql db by:   /usr/bin/postgresql-setup --initdb
4. systemctl start postgresql && systemctl status postgresql
5. when you successfully installed postresql you ll come to kno that a user named "postgres" automatically got created you can check by thi command
tail -1 /etc/passwd
or
cat /etc/passwd
6. Go to the user postgres: su - postgres
7. to create connection with postgres  use  "psql"
8. Run this query to create user & to provide sudo accesss to sonar user
postgres=# CREATE USER sonar WITH PASSWORD 'sonar' ;

postgres=# ALTER USER sonar WITH SUPERUSER
9. to check user is created or not
postgres=# \du  | you will find sonar user
\q |to quit
exit |to exit from normal user

We have to configure postgresql in such a way so that we provide sonar user access to this postgresql
:
vi /var/lib/pgsql/data/pg_hba.conf

   //replace peer and indent as = trust
   
10. connect to db by     su - postgres
psql
postgres=#  CREATE DATABASE sonar;                 //to store the analysis report
\l = to check db is created or not

11. now grant all the privileges on db sonar to sonar user to store the analysis report
postgres=#  GRANT ALL PRIVILEGES ON DATABASE sonar to sonar;

\l to check access
\q
exit
cd /opt
##download sonarqube using binaries##
wget ..................
and install java-openjdk11

It is mandatory and good practice to start sonar service using non root user
so create a user "sonar"
adduser sonar
*change ownership of sonarqube directory
chown -R sonar:sonar /opt/sonarqube
su - sonar
 cd /opt/sonarqube/
cd conf 
vi sonar.properties *


sonar.jdbc.username=sonar
sonar.jdbc.password=sonar
sonar.jdbc.url=jbdc:postgresql://localhost/sonar
sonar.path.data=/var/sonarqube/data
sonar.path.temp=/var/sonarqube/temp


again go to root user from sonar user
https://docs.sonarqube.org/latest/requirements/prerequisites-and-overview/

vi /etc/sysctl.conf

Paste thi
vm.max_map_count = 524288
fs.file-max = 131072


*sysctl -p    :it takes changes applied*


vi /etc/security/limits.conf

If the user running SonarQube (sonarqube in this example) does not have permission to have at least 131072 open descriptors, you must insert this line in /etc/security/limits.d/99-sonarqube.conf (or /etc/security/limits.conf as you wish):

sonar hard nofile 65535
sonar soft nofile 65535
:wq

su - sonar
cd /opt/sonar/bin
./sonar.sh start
 to check the status of sonar service        cd .. && cd logs
 cat sonar.log
 
 it not started cuz not apermission to create direcory in
 /var/sonarqube/data
 
 go to root user
 exit
 
 mkdir /var/sonarqube
 chown -R sonar:sonar /var/sonarqube
 su - sonar
 cd /opt/sonar/bin/
 ./sonar.sh start
 cd .. && cd logs
 cat sonar.log

or check the status by going to su -sonar
cd opt/sonar/bin
./sonar.sh status 



Chrome paste url of sonarqube :)


