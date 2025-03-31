Install Jenkins

Port Number- 8080

Centos:

yum update -y
yum install openjdk-21-jdk -y
java --version
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
yum install jenkins -y
systemctl start Jenkins
systemctl enable jenkins
systemctl status jenkins


Ubuntu:

apt install openjdk-21-jdk -y
java --version
wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
apt update -y
apt install jenkins -y
systemctl start jenkins
systemctl enable jenkins
systemctl status jenkins
-------------------------------------------------------------------------------------------------------------------------------------------
Install Maven

Download maven package https://maven.apache.org/download.cgi

mkdir /opt/maven
cd /opt/maven
wget https://dlcdn.apache.org/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.zip
unzip apache-maven-3.9.9-bin.zip
cd apache-maven-3.9.9
pwd
find / -name java-21*

vim ~/.bash_profile
--------------------------------------------->
JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64
M2_HOME=/opt/maven/apache-maven-3.9.9
M2=$M2_HOME/bin
PATH=$PATH:$HOME/bin:$JAVA_HOME:$M2
--------------------------------------------->
source ~/.bash_profile
mvn -v
------------------------------------------------------------------------------------------------------------------------------------------
Install Tomcat

Port Number- 8080

Download tomcat package from https://tomcat.apache.org/download-80.cgi

yum update -y
yum install openjdk-21-jdk -y
cd /opt
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.100/bin/apache-tomcat-9.0.100.tar.gz
tar -xvzf apache-tomcat-9.0.100.tar.gz
mv apache-tomcat-9.0.100 tomcat
cd tomcat
cd bin
./startup.sh
find / -name context.xml
vim /opt/tomcat/webapps/host-manager/META-INF/context.xml
vim /opt/tomcat/webapps/manager/META-INF/context.xml
cd bin
./shutdown.sh
./startup.sh
cd conf
vim tomcat-users.xml
----------------------------------------------------------------------------------------------------------->
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
<user username="deployer" password="deployer" roles="manager-script"/>
<user username="tomcat" password="s3cret" roles="manager-gui, admin-gui"/>
------------------------------------------------------------------------------------------------------------->
echo $PATH
ln -s /opt/tomcat/bin/shutdown.sh /usr/bin/tomcatdown
ln -s /opt/tomcat/bin/startup.sh /usr/bin/tomcatup
tomcatdown
tomcatup

#convert pfx file to JKS
# Go to $JAVA_HOME/bin directory
keytool -importkeystore -srckeystore Altudo.pfx -srcstoretype pkcs12 -destkeystore altudo.jks -deststoretype JKS

# Update value in the server.xml file

<Connector SSLEnabled="true" acceptCount="100" clientAuth="false" 
       disableUploadTimeout="true" enableLookups="false" 
       keystoreFile="conf/altudo.jks" keystorePass="mysecret" 
       maxThreads="150" port="443" 
       protocol="org.apache.coyote.http11.Http11NioProtocol" 
       scheme="https" secure="true" sslProtocol="TLS"/>

# Change the default <Host name="tomcat.altudo.net" to run tomcat https
# add below line deploy your apps to https

      <Host name="details.altudo.net"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
            <Context path="" docBase="mydetails-0.0.8" debug="0" privileged="true" />
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="app_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

# Update the value in web.xml file on the last section of </web-app>
    <security-constraint>
        <web-resource-collection>
            <web-resource-name>Entire Application</web-resource-name>
            <url-pattern>/*</url-pattern>
        </web-resource-collection>
        <user-data-constraint>
            <transport-guarantee>CONFIDENTIAL</transport-guarantee>
        </user-data-constraint>
    </security-constraint>
------------------------------------------------------------------------------------------------------------------------------------------
Install Nexus
Port Number- 8081

# Download nexus from latest https://help.sonatype.com/en/download.html

yum update -y
yum install openjdk-21-jdk -y
cd /opt
wget https://download.sonatype.com/nexus/3/nexus-unix-x86-64-3.78.1-02.tar.gz
tar -zxvf nexus-unix-x86-64-3.78.1-02.tar.gz
mv /opt/nexus-3.78.1-02 /opt/nexus
adduser nexus
visudo
chown -R nexus:nexus /opt/nexus
chown -R nexus:nexus /opt/sonatype-work
vim /opt/nexus/bin/nexus.rc
--------------------------------------------------------->
run_as_user="nexus"
--------------------------------------------------------->

vim /opt/nexus/bin/nexus.vmoptions
----------------------------------------->
-XX:MaxDirectMemorySize=1024m
-Djava.net.preferIPv4Stack=true
----------------------------------------->

vim /etc/systemd/system/nexus.service
----------------------------------------->
[Unit]
Description=nexus service
After=network.target

[Service]
Type=forking
LimitNOFILE=65536
ExecStart=/opt/nexus/bin/nexus start
ExecStop=/opt/nexus/bin/nexus stop
User=nexus
Restart=on-abort

[Install]
WantedBy=multi-user.target
---------------------------------------->
systemctl daemon-reload
systemctl start nexus.service
systemctl enable nexus.service
systemctl status nexus.service
------------------------------------------------------------------------------------------------------------------------------------------
Install SonarQube

Port Number- 9000

yum update -y
vim /etc/sysctl.conf
-------------------------->
vm.max_map_count=262144
fs.file-max=65536
--------------------------->
sysctl -p
yum install openjdk-17-jdk -y
yum install docker-compose -y
service docker start
mkdir sonar
cd sonar
vim docker-compose.yaml
----> use docker-compose.yaml attached in this repo
vim default.conf
-----> use default.conf attached in this repo
vim sonarqube.crt
------> update the crt file value in this file
vim sonarqube.key
-------> update the key file value in this file
docker-compose up -d
docker-compose logs --follow
------------------------------------------------------------------------------------------------------------------------------------------
