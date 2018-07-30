# Deploy multiple Laravel projects to CentOS server.

This note shows my mistakes in early stage of using Laravel due to lack of understanding the way this framework works. This also shows my solution and my experience after solving this problem .<br>

Tags: Laravel 5.4, PHP 5.6, CentOS 7, PostgreSQL 9.3, Git.

## Problem

In the beginning, I deploy my project to Apache by using Git to clone and pull a project from my PC. Later, I used `php artisan serve --host=110.xxx.xx.xx --port=8080 ` for starting my website and it works well in any scence. Afterward, I try to deploy other project to the same server by running the same command but on an other port `php artisan serve --host=110.xxx.xx.xx --port=8181 `. Although it run normally but I cannot access the second one. I only access the second when I stop to serve the first. So the question in my mind at that time is:<br>

> How can we run multiple Laravel projects (multiple websites) in the same host?


## Where is my solution?

Firstly, I try to find answers in [Stackoverflow](https://stackoverflow.com/) and [Laracast](https://laracasts.com/) for the question *"How to serve multiple Laravel website by using artisan?"*. Although I found many solutions, those are related to openning a VirtualHost on serve. Those are not my expected solution so I try to find another way.<br>

After reading a ton of posts, I have found the good way deploy our app to Apache without using `php artisan`. In this way, we will use   `composer dump-autoload` for loading and rending php page from laravel statements. The detail of this way and some sub problems happing when I conduct it are shown step by step in the next section.


## Current solution

[ running environment CentOS7]

1. Clone a project to server by using `git clone`
2. Set **apache** as ownner of the root project and `chmod` to set access permision for **www**
    * `sudo chown -R apache:apache [path_to_project]`
    * `sudo chmod 775 [path_to_project]/storage`
    * `sudo chmod 775 [path_to_project]/boostrap/cache`
3. If **SElinux** is enable on server, we have to add permission for accessing `project/storage` as well as `database` 
    * `chcon -R -t httpd_sys_rw_content_t [path_to_project]/storage`
    * `setsebool -P httpd_can_network_connect_db on`
4. For `routing`, we have to revise apache config file `httpd.config`. It lets `htaccess` file of Laravel can works
    * `vim [path_to_httpd_config_folder]/httpd.conf
    * Change `AllowOverride None` to  `AllowOverride All` and uncommand `Options Indexes FollowSymLinks`
5. Restart apache by using `service httpd restart`

6. Now, jump into your project and run some commands:
   * Clear cache by using `php artisan cache:clear`
   * Reload class file by using `composer dump-autoload`
7. Afterward, create a shorcut directory in `/var/www/html/[sitename]` and link to directory '[path_to_project]/public'
   * Using create soft link command `ln -s [path_to_project]/public var/www/html/[sitename]`
8. Test a site by accessing [domain]/[sitename]

#### Sub problem: `php artisan storage:link`

Although I have run `php aritisan storage:link`, we cannot access to any sub directory inside of `[path_to_project]/public/storage`.<br>

I solved this problem by using create softlink command manually and for making sure I also set the permision for appache to access the new created dir.
  * `ln -s [path_to_project]/storage/app/public/ [path_to_project]/public/storage`
  * `[path_to_project]/public/storage`


## Conclusion and new issues

Now I can run multiple website by redo all steps in previous session. However I wonder that is it possible for compiling project in my own PC and only upload `public` folder to server? and does it works well if there is different PHP version between PC and Host? <br>

For those questions I open 2 new issues:

* Deploy Laravel project to server using built `public` dir from local.
* Can Laravel website works on different PHP version using built `public` dir ?
