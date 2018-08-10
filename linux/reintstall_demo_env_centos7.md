# Re-install demo enviroment on CentOS7 [KYDEVEL]

This post use for re-setting up the environment for laravel app (5.6) on CentOS 7.0 from zero.

## Requirement applications:
 * PHP ^7.0
 * Composer
 * Apache
 * PostgreSQL
 * Git
 * SQL server driver
## Process
### APACHE
  * Install httpd by using `yum install httpd`
  * Start httpd service `sudo systemctl start httpd.service`
  * SELinux is disabled on the Vultr CentOS 7 x64 server instance by default. If you turn it on manually, you need also configure SELinux as below: 
     * `sudo setsebool -P httpd_can_network_connect on` 
     * `sudo setsebool -P httpd_can_network_connect_db on`
### PHP
  * Install wget by using `yum install wget`
  * Install remi repo by following this post http://www.servermom.org/how-to-enable-remi-repo-on-centos-7-6-and-5/2790/
     * In case it lack of EPEL dependency, please install it by using https://fedoraproject.org/wiki/EPEL 
  * Install php `yum --enablerepo=remi-php72 install php php-mcrypt php-cli php-gd php-curl php-mysql php-pgsql php-ldap php-zip php-fileinfo php-mbstring php-xml`


### PostgreSQL 9.5
Use the following commands to install PostgreSQL 9.5:
  * `sudo yum install http://yum.postgresql.org/9.5/redhat/rhel-7-x86_64/pgdg-centos95-9.5-2.noarch.rpm`
  * `sudo yum groupinstall "PostgreSQL Database Server 9.5 PGDG"`
  
Initiate the database:
  * `sudo /usr/pgsql-9.5/bin/postgresql95-setup initdb`
  
Setup database user authentication method:

  * `sudo vi /var/lib/pgsql/9.5/data/pg_hba.conf`
  * Find the and revise following section:
```
[FIND]
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                ident

[REVISE]
Modify the authentication method of IPv4 local connections to md5:

# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
Save and quit:
:wq
```

Setup PostgreSQL listening addresses:

  * `sudo vi /var/lib/pgsql/9.5/data/postgresql.conf`
  * Find the and revise following section:
```
Find:

#listen_addresses = 'localhost'
modify it to:

listen_addresses = '*'
Find:

#port = 5432
modify it to:

port = 5432
Save and quit:

:wq
```
Start the PostgreSQL service:

  * `sudo systemctl start postgresql-9.5.service`
  * `sudo systemctl enable postgresql-9.5.service`
  
Access postgreSQL by using:

  * `sudo -u postgres psql`
  
## Composer

  * Download composer `sudo curl -sS https://getcomposer.org/installer | php`
  * Make it globlaly for easy using. We move it to `bin` dir and rename it by using `mv composer.phar /usr/local/bin/composer`
  * In case , `/usr/local/bin` is not exist in $PATH`, export it by using `mv composer.phar /usr/local/bin/composer`
  * Check composer verison command is `composer -V`
  
## GIT
  * Install it by using `sudo yum install git`
  
## JAVA 8 

  * Install Java via yum `sudo yum install java-1.8.0-openjdk.x86_64`
  * Check the result by using `java -version`
  * Edit or create enviroment file and add JAVA_HOME:
    * `vi /etc/environment`
    * View Java root path by typing `sudo update-alternatives --config java`
    * Add and save to `environtment` file `JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre"`
    * Edit `bash_profile` file : `vi ~/.bash_profile`
    * Add and export `PATH` variable : `export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.111-1.b15.el7_2.x86_64/jre` and `export PATH=$JAVA_HOME/bin:$PATH` [*notice : no space in sentence]
    * Rerun bash file : `source ~/.bash_profile`
    * Check it : `echo $JAVA_HOME`
## TOMCAT 9

  * Create a new tomcat group: `sudo groupadd tomcat`
  * Create a tomcat user `sudo useradd -M -s /sbin/nologin -g tomcat -d /opt/tomcat tomcat` (member of the tomcat group, home directory of /opt/tomcat (install), shell of /bin/false (nobody login))
  * Create develoment dir for download tomcat
    * `cd ~`
    * `mkdir development`
    * `wget http://ftp.jaist.ac.jp/pub/apache/tomcat/tomcat-9/v9.0.10/bin/apache-tomcat-9.0.10.tar.gz` (check new verion on https://tomcat.apache.org/)
  * Install tomcat to the `/opt/tomcat` directory
    * `sudo mkdir /opt/tomcat`
    * `sudo tar xvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1` (C | update permissions :: proper access to the tomcat installation)
  * Jump into woring dir : `cd /opt/tomcat`
  * Change tomcat group ownership over the entire installation directory: `sudo chgrp -R tomcat /opt/tomcat`
  * Change tomcat group read access to the conf directory, and execute access to the directory:
    *`sudo chmod -R g+r conf`
    *`sudo chmod g+x conf`
  * Make the tomcat user the owner of the directories: `sudo chown -R tomcat webapps/ work/ temp/ logs/`
  * Create service file for tomcat:
    * Create and open unit file service: `sudo vi /etc/systemd/system/tomcat.service`
    * Paste the content of tomcat.service  as follow:
           
                # Systemd unit file for tomcat
                [Unit]
                Description=Apache Tomcat Web Application Container
                After=syslog.target network.target

                [Service]
                Type=forking

                Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre
                Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
                Environment=CATALINA_HOME=/opt/tomcat
                Environment=CATALINA_BASE=/opt/tomcat
                Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
                Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom -Duser.language=en -Duser.region=US'

                ExecStart=/opt/tomcat/bin/startup.sh
                ExecStop=/bin/kill -15 $MAINPID

                User=tomcat
                Group=tomcat
                UMask=0007
                RestartSec=10
                Restart=always

                [Install]
                WantedBy=multi-user.target
 
    * Reload Systemd to load the tomcat unit file: `sudo systemctl daemon-reload`
    * Start tomcat service: `sudo systemctl start tomcat`
    * View status of tomcat: `sudo systemctl status tomcat`
    * Enable the tomcat service start on server boot (optional):  `sudo systemctl enable tomcat`
    * Change de port of tomcat webserver in conflicts: `sudo vi /opt/tomcat/conf/server.xml` and search for <Connector port="8080" ...
    * Add tomcat user in tomcat-users.xml file: `sudo vim /opt/tomcat/conf/tomcat-users.xml`
     * Add line `<role rolename="manager-gui"/>` and `<user username="[yourusername]" password="[yourpass]" roles="manager-gui"/>`
    * Remove restriction in access to the tomcat manager by COMMENT in 2 files : `/opt/tomcat/webapps/manager/META-INF/context.xml` and `/opt/tomcat/webapps/host-manager/META-INF/context.xml` as follows
    
                <!--  <Valve className="org.apache.catalina.valves.RemoteAddrValve" 
                     allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->   
    
    * Restart tomcat `systemctl restart tomcat`
   

## Firewall setting 

  * Public port for Apache: 
     * `sudo firewall-cmd --permanent --add-port=80/tcp` 
     * `sudo firewall-cmd --permanent --add-port=443/tcp`
     * `sudo firewall-cmd --reload`
     * (Restart Apache )
  * Public port for PostgreSQL: 
     * `sudo firewall-cmd --zone=public --permanent --add-service=http`
     * `sudo firewall-cmd --zone=public --permanent --add-port=5432/tcp`
     * `sudo firewall-cmd --reload`
     * (Restart PostgreSQL)
  * Public port for Tomcat:
     * `sudo firewall-cmd --zone=public --permanent --add-port=8080/tcp`
     * `sudo firewall-cmd --reload`

