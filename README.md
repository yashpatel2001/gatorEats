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
Create a new file database_connection.php in your app directory.
Add the following code to the file:
```
<?php

use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\SSH;
use Jetturtle\RemoteDb\SshTunnelMySql;

function establishSshTunnelAndDbConnection() {
    SSH::into('cise')->forwardTo('mysql.cise.ufl.edu:3306', '127.0.0.1:3306', function ($tunnel) {
        // Connect to the database using the SSH tunnel
        $config = [
            'user'     => 'your_database_username',
            'password' => 'your_database_password',
            'database' => 'your_database_name',
            'host'     => '127.0.0.1',
            'port'     => $tunnel->getMappedPort(),
        ];
        DB::connection('cise_mysql')->update($config);
    });
}
``` 
Then add the following line to your bootstrap/app.php file to include the database_connection.php file in your Laravel application's bootstrap process:
```
require_once __DIR__.'/../app/database_connection.php';
``` 
Call the establishSshTunnelAndDbConnection() function wherever you need to connect to the database using the SSH tunnel.

## Set up basic routes 
To create a new route in your routes/api.php file:

Define a route like so 

```
Route::get('/data', 'DataController@index');
``` 

This route will trigger the index method of the DataController when accessed.
DataController is a controller. A controller is a PHP class that contains methods to handle incoming HTTP requests and return an HTTP response. Controllers provide a way to organize and centralize the logic for a particular set of related routes.

Next, create a new controller using the php artisan make:controller command:

php artisan make:controller DataController

This will create a new DataController.php file in the app/Http/Controllers directory. Open the DataController.php file and add the following code:
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Data;

class DataController extends Controller
{
    public function index()
    {
        // Fetch data from the database using the Data model
        $data = Data::all();

        // Return the data as a JSON response
        return response()->json($data);
    }
}
```

In this example, we're using the Data model to fetch all data from the database and returning it as a JSON response.
Next, create a new model using the php artisan make:model command:

php artisan make:model Data

This will create a new Data.php file in the app/Models directory. Open the Data.php file and add the following code:
In the Data model, call the establishSshTunnelAndDbConnection() function before performing any database operations:

```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Data extends Model
{
    use HasFactory;

    public static function fetchDataFromRemoteDatabase()
    {
        try {
            establishSshTunnelAndDbConnection();

            // Perform a SELECT query to fetch data from a table
            $data = DB::connection('cise_mysql')->table('your_table')->get();

            return $data;
        } catch (Exception $e) {
            // Handle the exception
        }
    }
}
```
Now you can call Data::fetchDataFromRemoteDatabase() to fetch data from the remote database using the SSH tunnel.
