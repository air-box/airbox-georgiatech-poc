# AirBox - Goergia Tech - Proof of Concept

It includes software, code, steps and evaluations of AirBox solution at Georgia Tech using the incubator environment.

## AirBox

## Georgia Tech Incubator Environment

## Deploying a model GT Enterprise app in GT Incubator Environment

A GT enterprise app includes the following components:
1. Load balancer (F5 Big IP)
2. App server (Apache Tomcat)

Both of these are connected through Georgia Tech network. Only loadbalancers have external IPs with App servers only accessible over internal IPs. 
Typically, external TLS is terminated in load balancers. The communication between load balancer and app server is also over TLS.

### Deploying App server (Apache Tomcat)
* Setup Apache Tomcat Server

  * Install Open-jdk-11
  ```
  $ sudo dnf update
  $ sudo dnf install java-11-openjdk java-11-openjdk-devel
  ```
  * Add Tomcat group
  ```$ sudo groupadd tomcat```

  * Create Tomcat directory
  ```$ sudo mkdir /opt/tomcat```

  * Create tomcat user, disable login and give rights
  ```$ sudo useradd -s /bin/nologin -g tomcat -d /opt/tomcat tomcat```

  * Download tomcat binary package
  ```
  $ sudo dnf install wget
  $ sudo wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.0.21/bin/apache-tomcat-10.0.21.tar.gz
  ```
  
  * Untar tomcat
  ```sudo tar -xvf apache-tomcat-10.0.21.tar.gz -C /opt/tomcat --strip-components=1```

  * Set permissions
  ```
  sudo chown -R tomcat: /opt/tomcat
  sudo sh -c 'chmod +x /opt/tomcat/bin/*.sh'
  ```

  * NOTE: Typically after this step you setup a tomcat service by creating tomcat.service file and enable it using systemctl.
  Ensure that service mode is off for purposes of this PoC demo.

* Setup tomcat-native
  * Install Openssl with headers
  ```$ dnf Install openssl-level```
  * Install apr with headers
  ```$ dnf Install apr-devel```
  * Install build essentials
  ```$ dnf group install "Development Tools"```
  * Download and install tomcat-native library
  ```
  $ mkdir /opt/tomcat-native
  $ wget https://dlcdn.apache.org/tomcat/tomcat-connectors/native/1.2.33/source/tomcat-native-1.2.33-src.tar.gz
  $ tar -xvf tomcat-native-1.2.33-src.tar.gz -C /opt/tomcat-native
  $ ./configure configure —with-apr=/usr/bin/apr-1-config —with-ssl=yes prefix=$CATALINA_HOME
  ```
  * Configure tomcat to use it
  $ Create or edit $CATALINA_HOME/bin/setenv.sh to add tomcat native to LD_LIBRARY_PATH
  ```
  CATALINA_HOME=/opt/tomcat
  LD_LIBRARY_PATH=/opt/air-box/:$LD_LIBRARY_PATH:$CATALINA_HOME/lib
  export LD_LIBRARY_PATH
  ```
 * Configure tomcat to handle HTTPS connections
  * Create and add SSL certs and keys and out them in the directory /opt/tomcat/ssl/ 
  * Add HTTPS APR connector in $CATALINA_HOME/conf/server.xml
  ```
  <Connector
    	protocol="org.apache.coyote.http11.Http11AprProtocol"
    	port="8443"
    	maxThreads="150"
    	SSLEnabled="true" >
 	<SSLHostConfig>
    		<Certificate
        	certificateKeyFile="ssl/server.key"
        	certificateFile="ssl/server.crt"
        	type="RSA"
        	/>
  	</SSLHostConfig>
    </Connector>
    ````
  ```
  
