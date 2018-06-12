# Sql server driver for PHP 7.2 on CentOS 7

This problem happen because using PHP 5.6 on CentOS 7 and sqlsvr is not supported for this version. 

I haved upgraded PHP 5.6 to 7.2 and install sqlsvr as follows.

## Upgrade PHP 7.2
*   **Step1**: Enable Remi repo for PHP 7.2
    `sudo yum-config-manager --enable remi-php72`
*   **Step2**:  Update repo
    `sudo yum update`
*   **Step3**: Check PHP version after upgraded
    `php -v`
    
## Install sqlsrv drivers
*   **Step1**: Install sqlsrv
    ` yum --enablerepo=remi,remi-php72 install php-sqlsrv`
*   **Step2**:  Check php ini setting
    `php --ini`
*   **Step3**: Restart apache 
    `service httpd stop` and `service httpd start` 
    *Note: I do not know why the command `service httpd restart` cannot help `php` reload its driver.
    
There is more command used when updating but fails:
  1.  `yum --enablerepo=remi,remi-php72 install php72-php-sqlsrv`
  2.  ` echo "extension= /usr/lib64/php/modules/pdo_sqlsrv.so" > /etc/php.d/pdo_sqlsrv.ini`
  3.  ` echo "extension= /usr/lib64/php/modules/sqlsrv.so" > /etc/php.d/sqlsrv.ini`
  

