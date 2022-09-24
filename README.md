# Project-14
EXPERIENCE CONTINUOUS INTEGRATION WITH JENKINS | ANSIBLE | ARTIFACTORY | SONARQUBE | PHP


### To  Install SonarQube on Ubuntu 20.04 With PostgreSQL as Backend Database manually 

- - Here is a manual approach to installation of SonarQube 7.9.3 version.
- Prerequisite 1 - to have Java installed since the tool is Java-based and using  PostgreSQL as backend database

- Prerequisite  - Tune Linux Kernel --To make a permanent change, edit the 

` sudo vi /etc/security/limits.conf ` 

  and append the below
- sonarqube   -   nofile   65536 
- sonarqube   -   nproc    4096

### Before installing, let us update and upgrade system packages:

- sudo apt-get update
- sudo apt-get upgrade
- Install wget and unzip packages

- sudo apt-get install wget unzip -y
- Install OpenJDK and Java Runtime Environment (JRE) 11

 - sudo apt-get install openjdk-11-jdk -y
 - sudo apt-get install openjdk-11-jre -y

### Set default JDK – To set default JDK or switch to OpenJDK enter below command:

 - sudo update-alternatives --config java

### If you have multiple versions of Java installed, you should see a list like below:

---

Selection    Path                                            Priority   Status

------------------------------------------------------------

  0            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      auto mode

  1            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      manual mode

  2            /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java   1081      manual mode

  3            /usr/lib/jvm/java-8-oracle/jre/bin/java          1081      manual mode

Type "1" to switch OpenJDK 11

Verify the set JAVA Version:

java -version
Output

--- 
java -version

openjdk version "11.0.7" 2020-04-14

OpenJDK Runtime Environment (build 11.0.7+10-post-Ubuntu-3ubuntu1)

OpenJDK 64-Bit Server VM (build 11.0.7+10-post-Ubuntu-3ubuntu1, mixed mode, sharing)

--- 

### Install and Setup PostgreSQL 10 Database for SonarQube

- The command below will add PostgreSQL repo to the repo list:

- sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'


### Download PostgreSQL software

- wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add - 

### Install PostgreSQL Database Server

- sudo apt-get -y install postgresql postgresql-contrib

### Start PostgreSQL Database Server

- sudo systemctl start postgresql

### Enable it to start automatically at boot time

- sudo systemctl enable postgresql

### Change the password for default postgres user (Pass in the password you intend to use, and remember to save it somewhere)

- sudo passwd postgres

### Switch to the postgres user

- su - postgres

### Create a new user by typing

- createuser sonar

### Switch to the PostgreSQL shell

- psql

### Set a password for the newly created user for SonarQube database

- ALTER USER sonar WITH ENCRYPTED password 'sonar';

### Create a new database for PostgreSQL database by running:

- CREATE DATABASE sonarqube OWNER sonar;

### Grant all privileges to sonar user on sonarqube Database.

- grant all privileges on DATABASE sonarqube to sonar;

### Exit from the psql shell:

- \q

### Switch back to the sudo user by running the exit command.

- exit

### Install SonarQube on Ubuntu 20.04 LTS Navigate to the tmp directory to temporarily download the installation files

- cd /tmp && sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-7.9.3.zip

### Unzip the archive setup to /opt directory

- sudo unzip sonarqube-7.9.3.zip -d /opt

### Move extracted setup to /opt/sonarqube directory

- sudo mv /opt/sonarqube-7.9.3 /opt/sonarqube


### CONFIGURE SONARQUBE
- We cannot run SonarQube as a root user, if you run using root user it will stop automatically. The ideal approach will be to create a separate group and a user to run SonarQube

### Create a group sonar

- sudo groupadd sonar

### Now add a user with control over the ` /opt/sonarqube ` directory

 - sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar 
 - sudo chown sonar:sonar /opt/sonarqube -R

### Open SonarQube configuration file using your favourite text editor (e.g., nano or vi)

- sudo vi /opt/sonarqube/conf/sonar.properties

### Find the following lines:

#sonar.jdbc.username=

#sonar.jdbc.password=

### Uncomment them and provide the values of PostgreSQL Database username and password:

--------------------------------------------------------------------------------------------------
--- 

-# DATABASE

#

-# IMPORTANT:

-# - The embedded H2 database is used by default. It is recommended for tests but not for

-#   production use. Supported databases are Oracle, PostgreSQL and Microsoft SQLServer.

-# - Changes to database connection URL (sonar.jdbc.url) can affect SonarSource licensed products.

-# User credentials.

-# Permissions to create tables, indices and triggers must be granted to JDBC user.

-# The schema must be created first.

sonar.jdbc.username=sonar

sonar.jdbc.password=sonar

sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube

### Edit the sonar script file and set RUN_AS_USER

- sudo vi /opt/sonarqube/bin/linux-x86-64/sonar.sh

-# If specified, the Wrapper will be run as the specified user.

-# IMPORTANT - Make sure that the user has the required privileges to write

-#  the PID file and wrapper.log files.  Failure to be able to write the log

-#  file will cause the Wrapper to exit without any way to write out an error

-#  message.

-# NOTE - This will set the user which is used to run the Wrapper as well as

-#  the JVM and is not useful in situations where a privileged resource or

-#  port needs to be allocated prior to the user being changed.

RUN_AS_USER=sonar

### Now, to start SonarQube we need to do following: Switch to sonar user

- sudo su sonar

### Move to the script directory

- cd /opt/sonarqube/bin/linux-x86-64/

### Run the script to start SonarQube

- ./sonar.sh start

### Expected output shall be as:
--- 

Starting SonarQube...

Started SonarQube

### Check SonarQube running status:

- ./sonar.sh status

### Output below:

- ./sonar.sh status

- SonarQube is running (176483).

### To check SonarQube logs, navigate to /opt/sonarqube/logs/sonar.log directory

- tail /opt/sonarqube/logs/sonar.log

### Output sample

--- 

 INFO  app[][o.s.a.SchedulerImpl] Process[ce] is up

 INFO  app[][o.s.a.SchedulerImpl] SonarQube is up

---

-  You can see that SonarQube is up and running above

### Configure SonarQube to run as a systemd service

- Stop the currently running SonarQube service

` cd /opt/sonarqube/bin/linux-x86-64/` 

- Run the script to stop SonarQube

` ./sonar.sh stop `

### Create a systemd service file for SonarQube to run as System Startup.

 ` sudo vi /etc/systemd/system/sonar.service `

### Add the configuration below for systemd to determine how to start, stop, check status, or restart the SonarQube service.

--- 

[Unit]

Description=SonarQube service

After=syslog.target network.target

[Service]

Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start

ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar

Group=sonar

Restart=always

LimitNOFILE=65536

LimitNPROC=4096

[Install]

WantedBy=multi-user.target

-----

### Save the file and control the service with systemctl

- sudo systemctl start sonar
- sudo systemctl enable sonar
- sudo systemctl status sonar