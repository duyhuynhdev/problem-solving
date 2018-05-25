# Laravel Command Aggregation

This is a laravel commands board which are often used by me. I think it is good for aggregate some used command for quick searching.

|                          Function                             |                    Command                                               |
| --------------------------------------------------------------|:------------------------------------------------------------------------:| 
| Install laravel installer via Composer                        | `composer global require "laravel/installer"`                            |
| Create project via Laravel installer                          | `laravel new [projectname]`                                              |
| Create project via Composer                                   | `composer create-project --prefer-dist laravel/laravel [projectname]`    |
| Run local development server                                  | `php artisan serve`                                                      |
| Run development server on specific port and host              | `php artisan serve --host=110.xxx.xx.xx --port=8080"`                    |
| Enable maintenance mode                                       | `php artisan down`                                                       |
| Run maintenance mode w message                                | `php artisan down --message="Upgrading Database" --retry=60`             |
| Enable maintenance mode for all except some IP                | `php artisan down --allow=127.0.0.1 --allow=192.168.0.0/16`              |
| Disable maintenance mode                                      | `php artisan up`                                                         |
| Create database migration and database table                  | `php artisan make:migration create_users_table --create=users`           |
| Add column database table                                     | `php artisan make:migration add_votes_to_users_table --table=users`      |
| Run migration                                                 | `php artisan migrate`                                                    |
| Rollback last migration                                       | `php artisan migrate:rollback`                                           |
| Rollback a specific step                                      | `php artisan migrate:rollback --step=5`                                  |
| Rollback all                                                  | `php artisan migrate:reset`                                              |
| Rollback and re-migrate                                       | `php artisan migrate:refresh`                                            |
| Rollback and re-migrate with initial data in seeder           | `php artisan migrate:refresh --seed`                                     |
| Create seeder                                                 | `php artisan make:seeder UsersTableSeeder`                               |
| Run seeder to insert data into db                             | `php artisan db:seed --class=UsersTableSeeder` or `php artisan db:seed`  |
| Create Eloquen ORM                                            | `php artisan make:model Flight`                                          |
| Create Eloquen ORM with migration                             | `php artisan make:model Flight --migration` or `-m`                      |
| Create controller                                             | `php artisan make:controller PhotoController `                           |
| Create resource controller (with some defaut func)            | `php artisan make:controller PhotoController --resource`                 |
| Create API controller                                         | `php artisan make:controller API/PhotoController --api`                  |
| Create middleware                                             | `php artisan make:middleware CheckAge`                                   |
| Public storage disk                                           | `php artisan storage:link`                                               |

