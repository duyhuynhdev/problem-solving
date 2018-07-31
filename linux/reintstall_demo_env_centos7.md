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
  * install httpd by using `yum install httpd`
  * start service `sudo systemctl start httpd.service`
SELinux is disabled on the Vultr CentOS 7 x64 server instance by default. If you turn it on manually, you need also configure SELinux as below:
  * `sudo setsebool -P httpd_can_network_connect on` and `sudo setsebool -P httpd_can_network_connect_db on`
### PHP
  * install wget by using `yum install wget`
  * install remi repo by following this post http://www.servermom.org/how-to-enable-remi-repo-on-centos-7-6-and-5/2790/
    * In case it lack of EPEL dependency, please install it by using https://fedoraproject.org/wiki/EPEL 
  * install php `yum --enablerepo=remi-php72 install php php-mcrypt php-cli php-gd php-curl php-mysql php-pgsql php-ldap php-zip php-fileinfo php-mbstring php-xml`


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
