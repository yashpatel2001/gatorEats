<p align="center"><img src="https://res.cloudinary.com/dtfbvvkyp/image/upload/v1566331377/laravel-logolockup-cmyk-red.svg" width="400"></p>

<p align="center">
<a href="https://travis-ci.org/laravel/framework"><img src="https://travis-ci.org/laravel/framework.svg" alt="Build Status"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://poser.pugx.org/laravel/framework/d/total.svg" alt="Total Downloads"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://poser.pugx.org/laravel/framework/v/stable.svg" alt="Latest Stable Version"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://poser.pugx.org/laravel/framework/license.svg" alt="License"></a>
</p>

## About Laravel

Laravel is a web application framework with expressive, elegant syntax. We believe development must be an enjoyable and creative experience to be truly fulfilling. Laravel takes the pain out of development by easing common tasks used in many web projects, such as:

- [Simple, fast routing engine](https://laravel.com/docs/routing).
- [Powerful dependency injection container](https://laravel.com/docs/container).
- Multiple back-ends for [session](https://laravel.com/docs/session) and [cache](https://laravel.com/docs/cache) storage.
- Expressive, intuitive [database ORM](https://laravel.com/docs/eloquent).
- Database agnostic [schema migrations](https://laravel.com/docs/migrations).
- [Robust background job processing](https://laravel.com/docs/queues).
- [Real-time event broadcasting](https://laravel.com/docs/broadcasting).

Laravel is accessible, powerful, and provides tools required for large, robust applications.

## Learning Laravel

Laravel has the most extensive and thorough [documentation](https://laravel.com/docs) and video tutorial library of all modern web application frameworks, making it a breeze to get started with the framework.

If you don't feel like reading, [Laracasts](https://laracasts.com) can help. Laracasts contains over 1500 video tutorials on a range of topics including Laravel, modern PHP, unit testing, and JavaScript. Boost your skills by digging into our comprehensive video library.


## Instructions to set up REST API: 

If this is the first install of your API, you will need to install WAMP or XAMPP depending if you have Windows (WAMP) or MACOS (XAMPP) 
This ensures that you have mysql, php and apache installed and configured on your machine 

Follow the default install steps for WAMP and XAMP, 
reference these two links for the install process.

https://www.w3resource.com/php/installation/install-wamp.php
https://www.javatpoint.com/how-to-install-xampp-on-mac

Install Composer to create a laravel project using the link below to guide you. 

https://www.geeksforgeeks.org/how-to-install-php-composer-on-windows/

It will find the default location where PHP was installed so just put all defaults in. Make sure to add the PHP to your path 

Run 'composer' in your terminal/command prompt. Restart computer if necessary to ensure its been sucessfully installed

Next, navigate to your wamp64 directory and then cd into www 
this is the default directory for wamp that can host the api locally for you. 

Clone the project with git clone <remote_url> 

cd into project directory 

Run these two commands, npm install 
then after,composer install 


The above command creates/install all the necessary files and dependencies needed to run a laravel project the 'gatorEats' is the project title 

For more instructions, in terms of connecting the db instance to laravel that will be discussed in a README sent out later and added to the project repo


## Setting up SSH Tunnel & DB Connection  

Install the required dependencies using Composer:
composer require jetturtle/ssh-tunnel-mysql

Create a new SSH connection configuration in the config/remote.php file:
```
'cise' => [
    'host'      => 'storm.cise.ufl.edu',
    'username'  => 'your_username',
    'password'  => 'your_password',
    'key'       => '',
    'keyphrase' => '',
    'agent'     => '',
    'timeout'   => 10,
]
```
Replace your_username and your_password with your actual CISE username and password. 

Create a new database connection in the config/database.php file:
``` 
'cise_mysql' => [
    'driver'         => 'mysql',
    'url'            => '',
    'host'           => '127.0.0.1',
    'port'           => '3306',
    'database'       => 'your_database_name',
    'username'       => 'your_database_username',
    'password'       => 'your_database_password',
    'unix_socket'    => '',
    'charset'        => 'utf8mb4',
    'collation'      => 'utf8mb4_unicode_ci',
    'prefix'         => '',
    'prefix_indexes' => true,
    'strict'         => true,
    'engine'         => null,
    'options'        => extension_loaded('pdo_mysql') ? array_filter([
        PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
    ]) : [],
]
``` 
Replace your_database_name, your_database_username, and your_database_password with your actual values.

Use the following code in your Laravel application to connect to the MySQL server through the SSH tunnel:
```
use SSH;
use Jetturtle\RemoteDb\SshTunnelMySql;
use Illuminate\Support\Facades\DB;

SSH::into('cise')->forwardTo('mysql.cise.ufl.edu:3306', '127.0.0.1:3306', function ($tunnel) {
    // connect to the database using the SSH tunnel
    $config = [
        'user'     => 'your_database_username',
        'password' => 'your_database_password',
        'database' => 'your_database_name',
        'host'     => '127.0.0.1',
        'port'     => $tunnel->getMappedPort(),
    ];
    DB::connection('cise_mysql')->update($config);
});
```