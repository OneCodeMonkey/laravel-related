# laravel official doc

for tutorials' doc.

## 1.Prologue

### upgrade guide

#### upgrading to 5.7.0 from 5.6

##### updating dependencies

update your `laravel/framework` dependency to 5.7.* in your  `composer.json` file.

if you are using laravel passport, you should update your `laravel/passport` dependency to `^7.0` in your `composer.json` file.

Next, examine any 3rd party packages consumed by your application and verify you are using the proper version for laravel 5.7 support.

##### Application

###### the `register` method

likelihood of impact: very low

the unused `option` argument of the `Illuminate\Foundation\Application` class `register` method has been removed. If your are overriding this method, you should update your method's signature:

```php
/**
 * Register a service provider with the application
 *
 * @param  \Illuminate\Support\ServiceProvider|string $provider
 * @param bool $force
 * @return \Illuminate\Support\ServiceProvider
 */
public function register($provider, $force = false);
```

##### Artisan

###### Scheduled Job Connection & Queues

likelihood of impact: low

The `$schedule->job` method now respects the `queue` and `connection` properties on the job class if a connection / job is not explicitly passed into the `job` method.

Generally, this should be considered a bug fix: however, it is listed as a breaking change out of caution.

##### Assets

###### Asset Directory Flattened(扁平化)

likelihood of impact: none

For new Laravel 5.7 applications, the assets directory that contains the scripts and styles has been flattened into the `resources` directory. This will not affect existing applications and does not require changes to your existing applications.

However, if you wish to make this change, you should move all files from the `resources/assets/*` directory up one level:

​	from `resources/assets/js/*` to `resources/js/*`

​	from `resources/assets/sass/*` to `resources/sass/*`

then, update any references to the old directories in your `webpack.mix.js` file:

```js
mix.js('resources/js/app.js', 'public/js').sass('resources/sass/app.scss', 'public/css');
```

###### `svg` directory added

likelihood of impact: very high

A new directory, `svg`, was added to the `public` directory. It contains four svg files: `403.svg`, `404.svg`, `500.svg`, and `503.svg`, which are displayed on their respective error pages.

##### Authentication

###### The `Authenticate` middleware

likelihood of impact: low

The `authenticate` method of the `Illuminate\Auth\Middleware\Authenticate` middleware has been updated to accept the incoming `$request` as its first argument. If you are overriding this method in your own `Authenticate` middleware, you should update your middleware's signature:

```php
/**
 * Determine if the user is logged in to any of the given guards.
 *
 * @param  \Illuminate\Http\Request  $request
 * @param  array  $guards
 * @return void
 *
 * @throws \Illuminate\Auth\AuthenticationException
 */
protected function authenticate($request, array $guards)
```

###### The `ResetsPassword` Trait

likelihood of impact: low

The protected `sendResetResponse` method of the `ResetsPasswords` trait now accepts the incoming `Illuminate\Http\Request` as its first argument. If you are overriding this method, you should update your method's signature:

```php
/**
 * Get the response for a successful password reset.
 *
 * @param  \Illuminate\Http\Request $request
 * @param  string  $response
 * @return  \Illuminate\Http\RedirectResponse|\Illuminate\Http\JsonResponse
 */
protected function sendResetResponse(Request $request, $response)
```

###### The `SendsPasswordResetEmails` trait

likelihood of impact: low

The protected `sendResetLinkResponse` method of the `SendsPassowrdResetEmails` trait now accepts the incoming `Illuminate\Http\Request` as its first argument. If you are overriding this method, you should update your method's signature:

```php
/**
 * Get the response for a successful password reset link.
 *
 * @param  \Illuminate\Http\Request  $request
 * @param  string  $response
 * @return  \Illuminate\Http\Redirect\Response|\Illuminate\Http\JsonResponse
 */
protected function sendResetLinkResponse(Request $request, $response)
```

##### Authorization

###### The `Gate` contract

likelihood of impact: very low

The `raw` method was changed from `protected` to `public` visibility. In addition, it was added to `Illuminate\Contracts\Auth\Access\Gate` contract:

```php
/**
 * Get the raw result from the authorization callback.
 *
 * @param  string  $ability
 * @param  array|mixed  $arguments
 * @return  mixed
 */
public function raw($ability, $arguments = []);
```

if you are implementing this interface, you should add this method to your implementation.

###### The `Login` event

likelihood of impact: very low

The `__construct` method of `Illuminate\Auth\Events\Login` event has a new `$guard` argument:

```php
/**
 * create a new event instance
 *
 * @param  string $guard
 * @param  \Illuminate\Contracts\Auth\Authenticatable  $user
 * @param  bool  $remember
 * @return  void
 */
public function __construct($guard, $user, $remember) 
```

If you are dispatching this event manually within your application, you'll need to pass this new argument into the event's constructor. The following example passes the default framework guard to the login event:

```php
use Illuminate\Auth\Events\Login;

event(new Login(config('auth.defaults.guard'), $user, $remember));
```

##### Blade

##### The `or` operator removed

likelihood of impact: high

The Blade "or" operator has been removed in favor of PHP's built-in `??` operator, which has the same function.

```php
// laravel 5.6-
{{ $foo or 'default' }}
// laravel 5.7...
{{ $foo ?? 'default' }}
```

##### Cache

likelihood of impact: very high

A new `data` directory has been added to `storage/framework/cache`. You should create this directory in your own application:

```shell
mkdir -p storage/framework/cache/data
```

Then, add a `.gitignore` file to the newly created `data` directory:

```shell
cp storage/framework/cache/.gitignore storage/framework/cache/data/.gitignore
```

Finally, ensure that the `storage/framework/cache/.gitignore` file is updated as follows:

```shell
*
!data/
!.gitignore
```

##### Carbon

likelihood of impact: very low

Carbon `marcos` are now handled by the `Carbon` library directly instead of Laravel's extension of the library. 

##### Collections

###### The `split` method

likelihood of impact: low

The `split method` has been updated to always return the requested number of "groups", unless the total number of items in the original collection is less than the requested collection count. Generally, this should be considered a bug fix; however it's listed as a breaking change out of caution.

##### Cookie

`Factory` Contract method signature

likelihood of impact: very low

The signature of the `make` and `forever` methods of the `Illuminate\Contracts\Cookie\Factory` interface have been changed. If you are implementing this interface, you should update these methods in your implementation.

##### Database

###### The `softDeletesTz` Migration method

likelihood of impact: low

The schema table builder's `softDeletesTz` method now accepts the column name as its first argument, while the `$precision` has been moved to the second argument position:

```php
/**
 * Add a "deleted at" timestampTz for the table.
 * 
 * @param  string  $column
 * @param  int  $precision
 * @return  \Illuminate\Support\Fluent
 */
public function softDeletesTz($column = 'deleted at', $precision = 0)
```

##### The `ConnectionInterface` Contract

Likelihood of impact: very low

The `Illuminate\Database\ConnectionInterface` contract's `select` and `selectOne` method signatures have been updated to accommodate(适应) the new `$useReadPdo` argument:

```php
/**
 * Run a select statement and return a single result.
 *
 * @param  string  $query
 * @param  array  $bindings
 * @param bool  $useReadPdo
 */
public function selectOne($query, $bindings = [], $useReadPdo = true);

/**
 * Run a select statement against the database.
 *
 * @param  string  $query
 * @param  array  $bindings
 * @param  bool  $useReadPdo
 * @return  array
 */
public function select($query, $bindings = [], $useReadPdo = true);
```

In addition, the `cursor` method was added to the contract:

```php
/**
 * Run a select statement against the database and returns a generator.
 *
 * @param  string  $query
 * @param  array  $bindings
 * @param  bool  $useReadPdo
 * @return \Generator
 */
public function cursor($query, $bindings = [], $useReadPdo = true);
```

If you are implementing this interface, you should add this method to your implementation.

##### Migration Command Output

likelihood of impact: very low

The core migration commands have been updated to set the output instance on the migrator class. If you were overriding or extending the migration commands, you should remove references to `$this->migrator->getNotes()` and use `$this->migrator->setOutput($this->output)` instead.

##### Debug

###### Dumper classes

likelihood of impact: very low

The `Illuminate\Support\Debug\Dumper` and `Illuminate\Support\Debug\HtmlDumper` classes have been removed in favor of using Symfony's native variable dumpers: `Symfony\Component\VarDumper\VarDumper` and `Symfony\Component\VarDumper\Dumper\HtmlDumper`.

##### Eloquent

###### The `latest/oldest` methods

likelihood of impact: low

The Eloquent query builder's `latest` and `oldest` methods have been updated to respect custom "created at" timestamp columns that may be specified on your Eloquent models. Generally, this should be considered a bug fix; however it is listed as a breaking change out pf caution.

###### The `wasChanged` method

likelihood of impact: very low

An Eloquent model's changes are now available to the `wasChanged` method before firing the `updating` event. Generally, this should be considered a bug fix. However, it is listed as a breaking change out of caution.

##### PostgreSQL Special Float Values

likelihood of impact: low

PostgreSQL supports the float values `Infinity`, `-Infinity` and `NaN`.Prior to Laravel 5.7, these were cast to `0` when the Eloquent casting type for the column was `float`, `double` or `real`.

As of Laravel 5.7, these values will be cast to the corresponding PHP constants `INF`, `-INF`, and `NaN`.

##### Email Verification

likelihood of impact: Optional

if you choose to use Laravel's new email verification services, you will need to add additional scaffolding( adj. 脚手架形式的) to your application. First, add the `verificationController` to your application:

`App\Http\Controllers\Auth\VerificationController`.

You will also need to modify your `App\User` model to implement the `MustVerifyEmail` contract:

```php
<?php
namespace App;

use Illuminate\Notifications\Notifiable;
use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable implements MustVerifyEmail
{
    use Notifiable;
    // ...
}
```

In order to use the `verified` middleware so that only verified users may access a given route, you will need to update the `$routeMiddleware` property of your `app/Http/Kernel.php` file to include the new `verified` and `signed` middleware:

```php
// Within App\Http\Kernel class...
protected $routeMiddleware = [
    'auth' => \Illuminate\Auth\Middleware\Authenticate::class,
    'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
    'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
    'can' => \Illuminate\Auth\Middleware\Authorize::class,
    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    'throttle' => \Illuminate\Routing\Middleware\Throttle\Requests::class,
    'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
    'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
];
```

You will also need the verification view stub. This view should be placed at `resources/view/auth/verify.blade.php`. 

Next, your user table must contain an `email_verified_at` column to store the date and time that the email address was verified:

```php
$table->timestamp('email_verified_at')->nullable();
```

In order to send the email when a user is registered, you should register following events and listeners in your `App\Providers\EventServiceProvider` class:

```php
use Illuminate\Auth\Events\Registered;
use Illuminate\Auth\Listeners\SendEmailVerificationNotification;

/**
 * The event listener mappings for the application
 *
 * @var array
 */
protected $listen = [
    Registered::class => [
        SendEmailVerificationNotification::class,
    ],
];
```

Finally, when calling the `Auth::routes` method, you should pass the `verify` option to the method:

```php
Auth::routes(['verify' => true]);
```

##### FileSystem

###### `Filesystem` Contract Methods

likelihood of impact: low

The `readStream` and `writeStream` methods have been added to the `Illuminate\Contracts\Filesystem\Filesystem` contract. If you are implementing this interface, you should add these methods to your implementation.

##### Hashing

###### `Hash::check` Method

likelihood of impact: None

The `check` method now **optionally** checks if the algorithm of the hash matches the configured algorithm.

##### Mail

###### Mailable Dynamic Variable Casing

likelihood of impact: low

Variables that are dynamically passed to mailable views are now automatically "camel cased" , which makes mailable dynamic variable behavior consistent with dynamic view variables. Dynamic mailable variables are not a documented Laravel feature, so likelihood of impact to your application is low.

###### Template Theme

likelihood of impact: Medium

If you have customized the default theme styles used for Markdown mailable templates, you will need to re-publish and make your customizations again. The button color classes have been renamed from 'blue', 'green', and 'red' to 'primary', 'success', and 'error'.

##### Queue

###### `QUEUE_DRIVER` Environment Variable

likelihood of impact: very low

The `QUEUE_DRIVER` environment variable has been renamed to `QUEUE_CONNECTION`. This should not affect existing applications that you are upgrading unless you intentionally modify your `config/queue.php` configuration file to match Laravel 5.7's

###### `WorkCommand` Options

Likelihood of impact: very low

The `stop-when-empty` option was added to the `WorkCommand`. If you extend this command, you need to add `stop-when-empty` to `$signature` property of your class.

##### Routing

###### The `Route::redirect` method

likelihood of impact: high

The `Route::redirect` method new returns a `302` HTTP status code redirect. The `permanentRedirect` method has been added to allow `301` redirects.

```php
// Return a 302 redirect...
Route::redirect('/foo', '/bar');
// Return a 301 redirect...
Route::redirect('/foo', '/bar', 301);
// Return a 301 redirect...
Route::permanentRedirect('/foo', '/bar');
```

##### The `addRoute` method

likelihood of impact: low

The `addRoute` methods of the `Illuminate\Routing\Router` class has been changed from `protected` to `public`.

##### Validation

###### Nested Validation Data

likelihood of impact medium

In previous versions of Laravel, the `validate` method did not return the correct data for nested validation rules. This has been corrected in Laravel 5.7:

```php
$data = Validator::make([
    'person' => [
        'name' => 'Bob',
        'job' => 'worker',
    ];
], ['person.name' => 'required'])->validate();

dump($data);

// Prior Behavior...
['person' => ['name' => 'Bob', 'job' => 'Developer']]

// New Bahavior...
['person' => ['name' => 'Bob']]
```

##### The `Validator` Contract

likelihood of impact: very low

The `validate` method was added to the `Illuminate\Contracts\Validation\Validator` contract:

```php
/**
 * Run the validator's rules against its data.
 *
 * @return array
 */
public function validate();
```

If you are implementing this interface, you should add this method to your implementation.

##### Testing

likelihood of impact: medium

Laravel 5.7 introduces improved testing tools for artisan commands. By default, Artisan command output is now mocked. If you are relying on the `artisan` method to run commands as part of your test, you should use `Artisan::call` or define `public $mockConsoleOutput = false` as a property in your test class.

##### Miscellaneous

See other changes on Github [Laravel](<https://github.com/laravel/laravel>)

## 2.Getting Started

### 2.1 Installation

#### Installation

##### Server Requirements

The Laravel framework has a few system requirements. All of these requirements are satisfied by the Laravel Homestead virtual machine.

However, if you are not using this, you will need to make sure your server meets the following requirements:

1.PHP >= 7.1.1

2.OpenSSL PHP Extension

3.PDO PHP Extension

4.Mbstring PHP Extension

5.Tokenizer PHP Extension

6.XML PHP Extension

7.Ctype PHP Extension

8.JSON PHP Extension

9.BCMath PHP Extension

##### Installing Laravel

Laravel utilizes Composer to manage its dependencies. So, before using Laravel, make sure you have Composer installed on your machine.

###### Via Laravel Installer

First, download the Laravel installer using Composer:

```shell
composer global require laravel/installer
```

Make sure to place composer's system-wide vendor bin directory in your `$PATH` so the laravel executable can be located by your system. This directory exists in different locations based on your operating system; however, some common locations include:

1. macOS: $HOME/.composer/vendor/bin
2. GNU/Linux Distributions: $HOME/.config/composer/vendor/bin

Once installed, the `laravel new` command will create a fresh Laravel installation in the directory you  specified. For instance, `laravel new blog` will create a directory named `blog` containing a fresh Laravel installation with all of Laravel's dependencies already installed:

```shell
laravel new blog
```

###### Via Composer Create-Project

Alternatively, you may also install Laravel by issuing the Composer `create-project` command in your terminal:

```shell
composer create-project --prefer-dist laravel/laravel blog "5.7.*"
```

###### Local Development Server

If you have PHP installed locally and you would like to use PHP's built-in development server to serve your application, you may use the `serve` Artisan command. This command will start a development server at `http://localhost:8000`

```shell
php artisan server
```

##### Configuration

###### Public Directory

After installing Laravel, you should configure your web server's document / web root to be the `public` directory. The `index.php` in this directory serves as the front controller for all HTTP requests entering your application.

###### Configuration Files

All of the configuration files for the Laravel framework are stored in the `config` directory. Each option is documented, so feel free to look through the files and get familiar with the options available to you.

###### Directory Permissions

After installing Laravel, you may need to configure some permissions. Directories within the `storage` and the `bootstrap/cache` directories should be writable by your web server or Laravel will not run. If you are using the Homestead vm, these permissions should already be set.

###### Application Key

The next thing you should do after installing Laravel is set your application key to a random string. If you installing Laravel via Composer or the Laravel installer, this key has already been set for you by the `php artisan key:generate` command.

Typically, this string should be 32 characters long. The key can be set in the `.env` enviroment file. If you have not renamed the `.env.example` file to `.env`, you should do that now. If the application key is not set, your user sessions and other encrypted data won't be secure.

###### Addition Configuration

Laravel needs almost no other configuration out of the box. You are free to get started developing! However, you may wish to review the `config/app.php` file and its documentation. It contains several options such as `timezone` and `locale` that you may wish to change according to your application.

You may also want to configure a few additional components of Laravel, such as: 

`Cache`, 

`Database`,

`Session`

#### Web Server Configuration

##### Pretty URLs

###### Apache

Laravel includes a `public/.htaccess` file that is used to provide URLs without the `index.php` front controller in the path. Before serving Laravel with Apache, be sure to enable the `mod_rewrite` module so the `.htaccess` file will be honored by the server.

If the `.htaccess` file that ships with Laravel does not work with your Apache installation, try this alternative:

```json
Options +FollowSymLinks -Indexes
RewriteEngine On

RewriteConf %{HTTP:Authorization} .
RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.php [L]
```

###### Nginx

If you are using Nginx, the following directive in your site configuration will direct all requests to the `index.php` front controller:

```json
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```

### 2.2 Configuration

#### Introduction

All of the configuration files for the Laravel framework are stored in the `config` directory. Each option is documented, so feel free to look through the files and get familiar with the options available to you .

#### Environment Configuration

It is often helpful to have different configuration values based on the environment where the application is running. For example, you may wish to use a different cache driver locally than you do on your production server.

To make this a cinch, Laravel utilizes the `DotEnv` PHP  library by Vance Lucas. In a fresh Laravel installation, the root directory of your application will contain a `.env.example` file. If you install Laravel via Composer, this file will automatically be renamed to `.env`. Otherwise, you should rename the file manually.

Your `.env` file should not be commited too your application's source control, since each developer / server using your application could require a different environment configuration. Furthermore, this would be a security risk in the event an intruder gains access to your source control repository, since any sensitive credentials would get exposed.

If you are developing with a team, you may wish to continue including a `.env.example` file with your application.By putting placeholder values in the example configuration file, other developers on your team can clearly see which environment variables are needed to run your application. You may also create a `.env.testing` file. This file will override the `.env` file when running PHPUnit tests or executing Artisan commands with the `--env=testing` option.

##### Environment Variable Types

All variables in your `.env` files are parsed as strings, so some reserved values have been created to allow you to return a wider range of types from the `env()` function:

| `.env` value | `env()` value |
| ------------ | ------------- |
| true         | (bool) true   |
| (true)       | (bool) true   |
| false        | (bool) false  |
| (false)      | (bool) false  |
| empty        | (string)''    |
| (empty)      | (string)''    |
| null         | (null) null   |
| (null)       | (null) null   |

If you need to define an environment variable with a value that contains space, you may do so by enclosing the value in double quotes.

```ini
APP_NAME="myapp"
```

##### Retrieving Environment Configuration

All of the variables listed in this file will be loaded into the `$_ENV` PHP super-global when your application receives a request. However, you may use the `env` helper to retrieve values from these variables in your configuration files. In fact, if you review the Laravel configuration files, you will notice several of the options already using this helper:

```php
'debug' => env('APP_DEBUG', false),
```

The second value passed to the `env` function is the "default value". This value will be used if no environment variable exists for the given key.

##### Determining The Current Environment

The current application environment is determined via the `APP_ENV` variable from your `.env` file. You may access this value via the `environment` method on the `App` facade:

```php
$environment = App::environment();
```

You may also pass arguments to the `environment` method to check if the environment matches a given value. The method will return `true` if the environment matches any of the given values:

```php
if(App::environment('local')) {
    // the environment is local
}
if(App::environment(['local', 'staging'])) {
    // either local or staing
}
```

##### Hiding Environment Variables From Debug Pages

When an exception is uncaught and the `APP_DEBUG` environment variable is `true`, the debug page will show all environment variables and their contents. In some cases you may want to obscure certain variables. You may do this by updating the `debug_blacklist` option in your `config/app.php` configuration file.

Some variables are available in both the environment variables and the server / request data. Therefore, you may need to blacklist them for both `$_ENV` and `$_SERVER`:

```php
return [
    //...
    'debug_blacklist' => [
        '_ENV' => [
            'APP_KEY',
            'DB_PASSWORD',
        ],
        '_SERVER' => [
            'APP_KEY',
            'DB_PASSWORD',
        ],
        '_POST' => [
            'password',
        ],
    ].
];
```



#### Accessing Configuration Values

You may easily access your configuration values using the global `config` helper function from anywhere in your application. The configuration values may be accessed using "dot" syntax, which includes the name of the file and option you wish to access. A default value may also be specified and will be returned if the configuration option does not exist:

```php
$value = config('app.timezone');
```

To set configuration values at runtime, pass an array to the `config` helper:

```php
config(['app.timezone' => 'America/Chicago']);
```



#### Configuration Caching

To give your application a speed boost, you should cache all of your configuration files into a single file using the `config:cache` Artisan command. This will combine all of the configuration options for your application into a single file which will be loaded quickly by the framework.

You should typically run the `php artisan config:cache` command as part of your production deployment routine. The command should not be run during local development as configuration options will frequently need to be changed during the course of your application's development.

#### Maintenance Mode

When your application is in maintenance mode, a custom view will be displayed for all requests into your application. This makes it easy to "disable" your application while it is updating or when you are performing maintenance. A maintenance mode check is included in the default middleware stack for your application. If the application is in maintenance mode, a `MaintenanceModeException` will be thrown with a status code of 503.

To enable maintenance mode, execute the `down` Artisan command:

```shell
php artisan down
```

You may also provide `message` and `retry` options to the `down` command. The `message` value may be used to display or log a custom message, while the `retry` value will be set as the `Retry-After` HTTP header's value:

```php
php artisan down --message="Upgrading Database" --retry=60
```

Even while in maintenance mode, specific IP addresses or networks may be allowed to access the application using the command's `allow` option:

```php
php artisan down --allow=127.0.0.1 --allow=192.168.0.0/16
```

To close maintenance mode, use the `up` command:

```php
php artisan up
```

##### Maintenance Mode & Queues

While your application is in maintenance mode, no `queue jobs` will be handled. The jobs will continue to be handled as manual once the application is out of maintenance mode.

##### Alternatives To Maintenance Mode

Since maintenance mode requires your application to have several seconds of downtime, consider alternatives like `Envoyer` to accomplish zero-downtime deployment with Laravel.

### 2.3 Directory Structure

#### Introduction

The default Laravel application structure is intended to provide a great starting point for both large and small applications. But you are free to organize your application however you like. Laravel imposes almost no restrictions on where any given class is located - as long as Composer can autoload the class.

##### Where is the `models` directory?

When getting started with Laravel, many developers are confused by the lack of a `models` directory. However, the lack of such a directory is intentional. We find the word "models" ambiguous since it means many different things to many different people. Some developers refer to an application's "model" as the totality of all of its business logic, while others refer to "models" as classes that interact with a relational database.

For this reason, we choose to place Eloquent models in the `app` directory by default, and allow the developer to place them somewhere else if they choose. 

#### The Root Directory

##### `App`

The `app` directory contains the core code of your appllication. We will explore this directory in more detail soon. However almost all of the classes in your application will be in this directory.

##### `Bootstrap`

The `bootstrap` directory contains the `app.php` file which bootstraps the framework. This directory also houses a `cache` directory which contains framework generated files for performance optimization such as the route and services cache files.

##### `Config`

The `config` directory, as the name implies, contains all of your application's configuration files. It is a great idea to read through all of these files and familiarize yourself with all of the options available to you.

##### `Database`

The `database` directory contains your database migrations, model factories, and seeds. If you wish, you may also use this directory to hold on SQLite database.

##### `Public`

The `public` directory contains the `index.php` file, which is the entry point for all requests entering your application and configures autoloading. This directory also houses your assets such as images, JavaScripts, and CSS.

##### `Resources`

The `resources` directory contains your views as well as your raw, un-compiled assets such as LESS, SASS, or JavaScript. This directory also houses all of your language files.

##### `Routes`

The `routes` directory contains all of the route definitions for your application. By default, several route files are included with Laravel: `web.php`, `api.php`, `console.php`, `channels.php`.

The `web.php` file contains routes that the `RouteServiceProvider` places in the `web` middleware group, which provides session state, CSRF protection, and cookie encryption. If your application does not offer a stateless, RESTful API, all of your routes will most likely be defined in the `web.php` file.

The `api.php` file contains routes that the `RouteServiceProvider` places in the `api` middleware group, which provides rate limiting. These routes are intended to be stateless, so requests entering the application through these routes are intended to be authenticated via tokens and won't have access to session state.

The `console.php` file is where you may define all of your Closure based console commands. Each Closure is bound to a command instance allowing a simple approach to interacting with each command's IO methods. Even though this file does not define HTTP routes, it defines console based entry points(routes) into your application.

The `channels.php` file is where you may register all of the event broadcasting channels that your application supports.

##### `Storage`

The `storage` directory contains your compiled Blade templates, file based session, file caches, and other files generated by the framework. This directory is segregated into `app`, `framework`,  and `logs` directories. The `app` directory may be used to store any files generated by your application. The `framework` directory is used to store framework generated files and caches. Finally, the `logs` directory contains your application's log files.

The `storage/app/public` directory may be used to store user-generated files, such as profile avatars, that should be publicly accessible. You should create a symbolic link at `public/storage` which points to this directory. You may create the link using `php artisan storage:link` command.

##### `Tests`

The `tests` directory contains your automated tests. An example PHPUnit test is provided out of the box. Each test class should be suffixed with the word `Test`. You may run your tests using the `phpunit` or `php vendor/bin/phpunit` commands.

##### `Vendor`

The `vendor` directory contains your Composer dependencies.

#### The App Directory

The majority of your application is housed in the `app` directory. By default, this directory is namespaced under `App` and is autoloaded by Composer using the `PSR-4` standard.

The `app` directory contains a variety of additional directories such as `Console`, `Http` and `Providers`. Think of the `Console` and `Http` directories as providing an API into the core of your application. The HTTP protocol and CLI are both mechanisms to interact with your application, but don't actually contain application logic. In other words, they are two ways of issuing commands to your application. The `Console` directory contains all of your Artisan commands, while the `Http` directory contains your controllers, middleware, and requests.

A variety of other directories will be generated inside the `app` directory as you use the `make` Artisan commands to generate classes. So, for example, the `app/Jobs` directory won't exist until you execute the `make:job` Artisan command to generate a job class.

##### `Broadcasting`

The `Broadcasting` directory contains all of the broadcast channel classes for your application. These classes are generated using the `make:channel` command. This directory doesn't exist by default, but will be created for you when you create your first channel. To learn more about channels, check out the documentation on the pages behind(`Digging Deeper/Broadcasting`).

##### `Console`

The `Console` directory contains all of the custom Artisan commands for your application. These commands may be generated using the `make:command` command. This directory also houses your console kernel, which is where your custom Artisan commands are registered and your `Scheduled tasks` are defined.

##### `Events`

This directory doesn't exist by default, but will be created for you by the `event:generate` and `make:event` Artisan commands. The `Events` directory houses `event classes`. Events may be used to alert other parts of your application that a given action has occurred, providing a great deal of flexibility and decoupling.

##### `Exceptions`

The `Exceptions` directory contains your application's exception handler and is also a good place to place any exceptions thrown by your application. If you would like to customize how your exceptions are logged or rendered, you should modify the `Handler` class in this directory.

##### `Http`

The `Http` directory contains your controllers, middleware, and form requests. Almost all of the logic to handle requests entering your application will be placed in this directory.

##### `Jobs`

This directory doesn't exist by default, but will be created for you if you execute the `make:job` Artisan command. The `Jobs` directory houses the `queueable jobs` for your application. Jobs may be queued by your application or run synchronously within the current request lifecycle. Jobs that run synchronously during the current request are sometimes referred to as "commands" since they are an implementation of the `command pattern`

##### `Listeners`

This directory doesn't exist by default, but will be created for you if you execute the `event:generate` or `make:listener` Artisan commands. The `Listeners` directory contains the classes that handle your `events`. Event listeners receive an event instance and perform logic in response to the event being fired. For example, a `UserRegistered` event might be handled by a `SendWelcomeEmail` listener.

##### `Mail`

This directory doesn't exist by default, but will be created for you if you execute the `make:email` Artisan command. The `Mail` directory contains all of your classes that represent emails sent by your application. Mail objects allow you to encapsulate all of the logic of building an email in a single, simple class that may be sent using the `Mail:send` method.

##### `Notifications`

This directory doesn't exist by default, but will be created for you if you execute `make:notification` Artisan command. The `Notification` directory contains all of the "transactional" notifications that are sent by your application, such as simple notifications about events that happen within your application. Laravel's notification features abstracts sending notifications over a variety of drivers such as email, Slack, SMS, or stored in a database.

##### `Policies`

This directory doesn't exist by default, but will be created for you if you execute the `make:policy` Artisan command. The `Policies` directory contains the authorization policy classes for your application. Policies are used to determine if a user can perform a given action against a resource. For more information, check out the `Digging Deeper/authorization`.

##### `Providers`

The `Providers` directory contains all of the `service providers` for your application. Service providers bootstrap your application by binding services in the service container, registering events, or performing any other tasks to perpare your application for incoming requests.

In a fresh Laravel application, this directory will already contain several providers. You are free to add your own providers to this directory as needed.

##### `Rules`

This directory doesn't exist by default, but will be created for you if you execute the `make:rule` Artisan command. The `Rules` directory contains the custom validation rule objects for your application. Rules are used to encapsulate complicated validation logic in a simple object. For more information, check out the `Digging Deeper/validation`.

### 2.4 Homestead

[homestead usage](https://laravel.com/docs/5.7/homestead)

### 2.5 Valet

// 用不到

### 2.6 Deployment

#### Introduction

When you're ready to deploy your Laravel application to production, there are some important things your can do to make sure your application is running as efficiently as possible. In this document, we'll cover some great starting points for making sure your Laravel application is deployed properly.

#### Server Configuration

##### Nginx

If you are deploying your application to a server that is running Nginx, you may use the following configuration file as a starting point for configuring your web server. Most likely, this file will need to be customized depending on your server's configuration. If you would like assistance in managing your server, consider using a service such as `Laravel Force`:

```ini
server {
	listen 80;
	server_name test.com;
	root /test.com/public;
	
	add_header X-Frame-Options "SAMEORIGIN";
	add_header X-XSS-Protection "1; mode=block";
	add_header X-Content-Type-Options "nosniff";
	
	index index.html index.htm index.php;
	
	charset utf-8;
	
	location / {
        try_files $uri $uri/ /index.php?$query_string;
	}
	
	location = /favicon.ico {access_log off; log_not_found off;}
	location = /robots.txt {access_log off; log_not_found off;}
	
	error_page 404 /index.php;
	
	location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
	}
	
	location ~ /\.(?!well-known).* {
        deny all;
	}
} 
```



#### Optimization

##### Autoloader Optimization

When deploying to production environment, make sure that you are optimizing Composer's class autoloader map so Composer can quickly find the proper file to load for a given class:

```shell
composer install --optimize-autoloader --no-dev
```



##### Optimizing Configuration Loading

When deploying your application to production, you should make sure that you run the config:cache Artisan command during your deployment process:

```php
php artisan config:cache
```

This command will combine all of Laravel's configuration files into a single, cached file, which greatly reduces the number of trips the framework must make to the filesystem when loading your configuration values.

##### Optimizing Route Loading

If you are building a large application with many routes, you should make sure that you are running the `route:cache` Artisan command during your deployment process:

```shell
php artisan route:cache
```

This command reduces all of your route registrations into a single method call within a cached file, improving the performance of route registration when registering hundreds of routes.

#### Deploying With Forge

This is a 3-rd party service, place it behind if I have enough experience with usage of it.  :)

## 3.Architecture Concepts

### 3.1 Request LifeCycle

#### Introduction

 When using any tool in the real world, you feel more confident if you understand how that tool works. Application is also this. When you understand how your development tools function, you feel more comfortable and confident using them.

The goal of this document is to give you a good, high-level overview of how the Laravel framework works. By getting to know the overall framework better, everything feels less "magical" and you will be more confident building your applications. If you don't understand all of the terms right away, don't lose heart. Just try to get a basic grasp of what is going on, and your knowledge will grow as you explore other sections of the documentation.

#### Lifecycle Overview

##### First things

The entry point for all requests to a Laravel application is the `public/index.php` file. All requests are directed to this file by your web server(apache? nginx?) configuration. The `index.php` file doesn't contain much code. Rather, it is a starting point for loading the rest of the framework.

The `index.php` file loads the Composer generated autoloader definition, and then retrieves an instance of the Laravel application from `bootstrap/app.php` script. The first action taken by Laravel itself is to create an instance of the application / `service container`.

##### HTTP / Console Kernels

Next, the incoming request is sent to either the HTTP kernel or the console kernel, depending on the type of request that is entering the application. These two kernels serve as the central location that all requests flow through. For now, let's just focus on the HTTP kernel, which is located in `app/Http/Kernel.php`.

The HTTP kernel extends the `Illuminate\Foundation\Http\Kernel` class, which defines an array of `bootstrappers` that will be run before the request is executed. These bootstrappers configure error handling, configure logging, `Getting Started/Configuration#Environment configuration`, and platform other tasks that need to be done before the request is actually handled.

The HTTP kernel also defines a list of HTTP `The basics/Middleware` that all requests must pass through before being handled by the application. These middleware handle reading and writing the `The basics/Http session`, determining if the application is in maintenance mode, `verifying the CSRF token(The basics/CSRF Protection)`, and more.

The method signature for the HTTP kernel's `handle` method is quite simple: receive a `Request` and return a `Response`. Think of the Kernel as being a big black box that represents your entire application. Feed it HTTP requests and it will return HTTP responses.

##### Service Providers

One of the most important Kernel bootstrapping actions is loading the `service providers(Architecture Concepts/Service Provider)` for your application. All of the service providers for the application are configured in the `config/app.php` configuration file's `providers` array. First the `register` method will be called on all providers, then, once all providers have been registered, the `boot` method will be called.

Service providers are responsible for bootstrapping all of the framework's various components, such as the database, queue, validation, and routing components. Since they bootstrap and configure every feature offered by the framework, service providers are the most important aspect of the entire Laravel bootstrap process.

##### Dispatch Request

Once the application has been bootstrapped and all service providers have been registered, the `Request` will be handled off to the router for dispatching. The router will dispatch the request to a route or controller, as well as run any route specific middleware.

#### Focus on Service Providers

Service providers are truly the key to bootstrapping a Laravel application. The application instance is created, the service providers are registered, and the request is handled to the bootstrapped application. It's really that simple.

Having a firm grasp of "How a Laravel application is built and bootstrapped" via service providers is very valuable. Your application's default service providers are stored in the `app/Providers` directory.

By default, the `AppServiceProvider` is fairly empty. This provider is a great place to add your application's own bootstrapping and service container bindings. For large applications, you may wish to create several service providers, each with a more granular type of bootstrapping.

### 3.2 Service Container

#### Introduction

The Laravel service container is a powerful tool for managing class dependencies injection. Dependency injection is a fancy phrase that essentially means this: class dependencies are "injected" into the class via the constructor or, in some cases, "setter" methods.

Let's look at a simple example:

```php
<?php
namespace App\Http\Controllers;

use App\User;
use App\Repositories\UserRepository;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    /**
     * The user repository implementation
     *
     * @var  UserRepository
     */
    protected $users;
    
    /**
     * Create a new controller instance.
     *
     * @param  UserRepository  $user
     * @return  void
     */
    public function __construct(UserRepository $users)
    {
        $this->users = $users;
    }
    
    /**
     * show the profile for the given user.
     *
     * @param  int  $id
     * @return  Response
     */
    public function show($id)
    {
        $user = $this->users->find($id);
        return view('user.profile', ['user' => $user]);
    }
}
```

In this example, the `UserController` needs to retrieve users from a data source. So, we will inject a service that is able to retrieve users. In this context, out `UserRepository` most likely uses `Eloquent` to retrieve user information from the database. However, since the repository is injected, we are able to easily swap it out with another implementation. We are also able to easily "mock", or create a dummy implementation of the `UserRepository` when testing our application.

A deep understanding of the Laravel service container is essential to building a powerful, large application, as well as for contributing to the Laravel core itself.

#### Binding

##### Binding Basics

Almost all of your service container bindings will be registered within `service providers`, so most of these examples will demonstrate using the container in that context.

> There is no need to bind classes into the container if they do not depend on any interfaces. The container doesn't need to be instructed on how to build these objects, since it can automatically resolve these objects using reflection.

###### Simple Bindings

Within a service provider, you always have access to the container via the `$this->app` property. We can register a binding using the `bind` method, passing the class or interface name that we wish to register along with a `Closure` that returns an instance of the class:

```php
$this->app->bind('HelpSpot\API', function ($app) {
    return new HelpSpot\API($app->make('HttpClient'));
});
```

Note that we receive the container itself as an argument to the resolver. We can then use the container to resolve sub-dependencies of the object we are building.

###### Building A Singleton

The `singleton` method binds a class or interface into the container that should only be resolved one time. Once a singleton binding is resolved, the same object instance will be returned on subsequent calls into the container:

```php
$this->app->singleton('HelpSpot\API', function ($app) {
    return new HelpSpot\API($app->make('HttpClient'));
});
```

###### Binding Instances

You may also bind an existing object instance into the container using the `instance` method. The given instance will always be returned on subsequent calls into the container:

```php
$api = new HelpSpot\API(new HttpClient);
$this->app->instance('HelpSpot\API', $api);
```

###### Binding Primitives

Sometimes you may have a class that receives some injected classes, but also needs an injected primitive value such as an integer. You may easily use contextual binding to inject any value your class may need:

```php
$this->app->when('App\Http\Controllers\UserController')
    ->needs('$variableName')
    ->give($value);
```

##### Binding Interfaces To implementations

A very powerful feature of the service container is its ability to bind an interface to a given implementation. For example, let's assume we have an `EventPusher` interface and a `RedisEventPusher` implementation. Once we have coded our `RedisEventPusher` implementation of this interface, we can register it with the service container like so:

```php
$this->app->bind(
	'App\Contracts\EventPusher',
    'App\Services\RedisEventPusher'
);
```

This statement tells the the container that it should inject the `RedisEventPusher` when a class needs an implementation of `EventPusher`. Now we can type-hint the `EventPusher` interface in a constructor, or any other location where dependencies are injected by the service container:

```php
use App\Contracts\EventPusher;

/**
 * create a new class instance.
 *
 * @param  EventPusher  $pusher
 * @return  void
 */
public function __construct(EventPusher $pusher)
{
    $this->pusher = $pusher;
}
```

##### Contextual Binding

Sometimes you may have two classes that utilize the same interface, but you wish to inject different implementations into each class. For example, two controllers may depend on different implementations of the `Illuminate\Contracts\Filesystem\Filesystem` contract. Laravel provides a simple, fluent interface for defining this behavior:

```php
use Illuminate\Support\Facades\Storage;
use App\Http\Controllers\PhotoController;
use App\Http\Controllers\VideoController;
use Illuminate\Contracts\Filesystem\Filesystem;

$this->app->when(PhotoController::class)
    ->needs(Filesystem::class)
    ->give(function () {
    	return Storage::disk('local'); 
    });
$this->app->when([VideoController::class, UploadController::class])
    ->needs(Filesystem::class)
    ->give(function () {
        return Storage::disk('s3');
    });
```

##### Tagging

Occasionally, you may need to resolve all of a certain "category" of binding. For example, perhaps you are building a report aggregator that receives an array of many different `Report` interface implementations. After registering the `Report` implementations, you can assign them a bug using the `tag` method:

```php
$this->app->bind('SpeedReport', function () {
    //
});
$this->app->bind('MemoryReport', function () {
   	// 
});
$this->app->tag(['SpeedReport', 'MemoryReport'], 'reports');
```

Once the services have been tagged, you may easily resolve them all via the `tagged` method:

```php
$this->app->bind('ReportAggregator', function ($app) {
   	return new ReportAggregator($app->tagged('reports')); 
});
```

##### Extending Bindings

The `extend` method allows the modification of resolved services. For example, when a service is resolved, you may run additional code to decorate or configure the service. The `extend` method accepts a Closure, which should return the modified service, as its only argument:

```php
$this->app->extend(Service::class, function ($service) {
   	return new DecoratedService($service); 
});
```

#### Resolving

##### The Make Method

You may use the `make` method to resolve a class instance out of the container. The `make` method accepts the name of the class or interface you wish to resolve:

```php
$api = $this->app->make('HelpSpot\API');
```

If you are in a location of your code that doesn't have access to the `$app` variable, you may use the global `resolve` helper:

```php
$api = resolve('HelpSpot\API');
```

If some of your class' dependencies are not resolvable via the container, you may inject them by passing them as an associative array into the `makeWith` method:

```php
$api = $this->app->makeWith('HelpSpot\API', ['id' => 1]);
```

##### Automatic Injection

Alternatively, and importantly, you may "type-hint" the dependency in the constructor of a class that is resolved by the container, including `controllers`, `event listeners`, `queue jobs`, `middleware`, and more. In practice, this is how most of your objects should be resolved by the container.

For example, you may type-hint a repository defined by your application in a controller's constructor. The repository will automatically be resolved and injected into the class:

```php
<?php
namespace App\Http\Controllers;

use App\Users\Repository as UserRepository;

class UserController extends Controller
{
    /**
     * The user repository instance.
     */
    protected $users;
    
    /**
     * Create a new controller instance.
     *
     * @param  UserRepository  $users
     * @return  void
     */
    public function __construct(UserRepository $users)
    {
        $this->users = $users;
    }
    
    /**
     * show the user with the given ID
     *
     * @param  int  $id
     * $return  Response
     */
    public function show($id)
    {
        //
    }
}
```

#### Container Events

The service container fires an event each time it resolves an object. You may listen to this event using the `resolving` method:

```php
$this->app->resolving(function ($object, $app) {
   	// called when container resolves object of any type.. 
});
$this->app->resolving(HelpSpot\API::class, function ($api, $app) {
    // called when container resolves objects of type "HelpSpot\API"..
})
```

As you can see, the object being resolved will be passed to the callback, allowing you to set any additional properties on the object before it is given to its consumer.

#### PSR-11

Laravel's service container implements the `PSR-11` interface. Therefore, you may type-hint the PSR-11 container interface to obtain an instance of the Laravel container:

```php
use Psr\Container\ContainerInterface;

Route::get('/', function (ContainerInterface $container) {
   	$service = $container->get('Service');
    //
});
```



### 3.3 Service Providers

#### Introduction

Service providers are the central place of all Laravel application bootstrapping. Your own application, as well as all of Laravel's core services are bootstrapper via service providers.

But, what do we mean by "bootstrapped"? In general, we mean **registering** things, including registering service container bindings, event listeners, middleware, and even routes. Service providers are the central place to configure your application.

If we open the `config/app.php` file included with Laravel, you will see a `providers` array. These are all of the service provider classes that will be loaded for your application. Note that many of these are 'deferred' providers, meaning they will not be loaded on every request, but only when the services they provide are actually needed.

In this overview you will learn how to write your own service providers and register them with your Laravel application.

#### Writing Service Providers

All service providers extend the `Illuminate\Support\ServiceProvider` class. Most service providers contain a `register` and a `boot` method. Within the `register` method, you should only bind things into the `service container`. You should never attempt to register any event listeners, routes, or any other piece of functionality within the `register` method.

The Artisan CLI can generate a new provider via the `make:provider` command:

```shell
php artisan make:provider RiakServiceProvider
```



##### The Register Method

As mentioned previously, within the `register` method, you should only bind things into the `service container`. You should never attempt to register any event listeners, routes, or any other piece of functionality within the `register` method. Otherwise, you may accidentally use a service that is provided by a service provider which has not loaded yet.

Let's take a look at a basic service provider. Within any of your service provider methods, you always have access to the `$app` property which provides access to the service container:

```php
<?php
namespace App\Providers;

use Riak\Connection;
use Illuminate\Support\ServiceProvider;

class RiakServiceProvider extends ServiceProvider
{
    /**
     * Register bindings in the container.
     *
     * @return  void
     */
    public function register()
    {
        $this->app->singleton(Connection::class, function($app) {
            return new Connection(config('riak'));
        });
    }
}
```

This service provider only defines a `register` method, and uses that method to define an implementation of `Riak\Connection` in the service container. If you don't understand how the service contaienr works, check out the last chapter.

###### The `bindings` And `singletons` Properties

If your service provider registers many simple bindings, you may wish to use the `bindings` and `singleton` properties instead of manually registering each container binding. When the service provider is loaded by the framework, it will automatically check for these properties and register their bindings:

```php
<?php
namespace App\Providers;

use App\Contracts\ServerProvider;
use App\Contracts\DowntimeNotifier;
use Illuminate\Support\ServiceProvider;
use App\Services\PingdomDowntimeNotifier;
use App\Services\DigitalOceanServerProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * All of the container bindings that should be registered.
     *
     * @var  array
     */
    public $bindings = [
        ServerProvide::class => DigitalOceanServerProvider::class,
    ];
    
    /**
     * All of the container singletons that should be registered.
     *
     * @var  array
     */
    public $singletons = [
        DowntimeNotifier::class => PingdomDowntimeNotifier::class,
    ];
}
```



##### The Boot Method

So, what if we need to register a `view composer` within our service provider? This should be done within the `boot` method. **This method is called after all other service providers have been registered**, meaning you have access to all other services that have been registered by the framework:

```php
<?php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;

class ComposerServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return  void
     */
    public function boot()
    {
        view()->composer('view', function() {
            //
        })；
    }
}
```

###### Boot Method Dependency injection

You may type-hint dependencies for your service provider's `boot` method. The `service container` will automatically inject any dependencies you need:

```php
use Illuminate\Contracts\Routing\ResponseFactory;

public function boot(ResponseFactory $response)
{
    $response->macro('caps', function ($value) {
       	// 
    });
}
```



#### Registering Providers

All service providers are registered in the `config/app.php` configuration file. This file contains a `provider` array where you can list the class names of your service providers. By default, a set of Laravel core service providers are listed in this array. These providers bootstrap the core Laravel components, such as the mailer, queue, cache, and others.

To register your provider, add it to the array:

```php
'providers' => [
    // other service providers
    App\Providers\ComposerService\Provider::class,
],
```



#### Deferred Providers

If your provider is only registering bindings in the `service container`, you may choose to defer its registration until one of the registered bindings is actually needed. Deferring the loading of such a provider will improve the performance of your application, since it's not loaded from the filesystem on every request.

Laravel compiles and stores a list of all of the services supplied by deferred service providers, along with the name of its service provider class. Then, only when you attempt to resolve one of these services does Laravel load the service provider.

To defer the loading of a provider, set the `defer` property to `true` and define a `providers` method. The `provides` method should return the service container bindings registered by the provider:

```php
<?php
namespace App\Providers;

use Rial\Connection;
use Illuminate\Support\ServiceProviders;

class RiakServiceProvider extends ServiceProvider
{
    /**
     * Indicates if loading of the provider is deferred.
     *
     * @var  bool
     */
    protected $defer = true;
    
    /**
     * Register the service provider.
     *
     * @return void
     */
    public function register()
    {
        $this->app->singleton(Connection::class, function ($app) {
            return new Connection($app['config']['riak']);
        });
    }
    
    /**
     * Get the services provided by the provider.
     *
     * @return  array
     */
    public function provides()
    {
        return [Connection::class];
    }
}
```



### 3.4 Facades

#### Introduction

Facades provide a "static" interface to classes that are available in the application's `service container` Laravel ships with many facades which provide access to almost all of Laravel's features. Laravel facades serve as "static proxies" to underlying classes in the service container, providing the benefit of a terse, expressive syntax while maintaining more testability and flexibility than traditional static methods.

All of Laravel's facades are defined in the `Illuminate\Support\Facades` namespace. So, we can easily access a facade like this:

```php
use Illuminate\Support\Facades\Cache;

Route::get('/cache', function () {
    return Cache::get('key');
});
```

Throughout the Laravel documentation, many of the examples will use facades to demonstrate various features of the framework.

#### When To User Facades

Facades have many benefits. They provide a terse, memorable syntax that allows you to use Laravel's features without remembering long class names that must be injected or configured manually. Furthermore, because of their unique usage of PHP's dynamic methods, they are easy to test.

However, some care must be taken when using facades. The primary danger of facades is class scope creep. Since facades are so easy to use and don't require injection, it can be easy to let your classes continue to grow and use many facades in a single class. Using dependencies injection, this potential is mitigated by the visual feedback a large constructor gives you that your class is growing too large. So when using facades,  pay special attention to the size of your class so that its scope of responsibility stays narrow.

##### Facades & Dependency injection

One of the primary benefits of dependency injection is the ability to swap implementations of the injected class. This is useful during testing since you can inject a mock or stub and assert that various methods were called on the stub.

Typically, it would not be possible to mock or stub a truly static class method. However, since facades use dynamic methods to proxy methods calls to objects resolved from the service container, we actually can test facades just as we would test an injected class instance. For example, given the following route:

```php
use Illuminate\Support\Facades\Cache;

Route::get('/cache', function () {
    return Cache::get('key');
});
```

We can write the following test to verify that the `Cache::get` method was called with the argument we expected:

```php
use Illuminate\Support\Facades\Cache;

/**
 * A basic functional test example.
 *
 * @return  void
 */
public function testBasicExample()
{
    Cache::shouldReceive('get')
        ->with('key')
        ->andReturn('value');
    $this->visit('/cache')
        ->see('value');
}
```



##### Facades & Helper Functions

In addition to facades, Laravel includes a variety of "helper" functions which can perform common tasks like generating views, firing events, dispatching jobs, or sending HTTP responses. Many of these helper functions perform the same function as a corresponding facade. For example, this facade call and helper call are equivalent:

```php
return View::make('profile');

return view('profile');
```

There is absolutely no practical difference between facades and helper functions. When using helper functions, you may still test them exactly as you would the corresponding facade. For example, given the following route:

```php
Route::get('/cache', function () {
   	return cache('key'); 
});
```

There is absolutely no practical difference between facades and helper functions. When using helper functions, you may still test them exactly as you would the corresponding facade. For example, given the following route:

```php
Route::get('/cache', function () {
   	return cache('key'); 
});
```

Under the hood, the `cache` helper is going to call the `get` method on the class underlying the `Cache` facade. So, even though we are using the helper function, we can write the following test to verify that te method was called with the argument we expected:

```php
use Illuminate\Support\Facades\Cache;

/**
 * A basic functional test example.
 *
 * @return  void
 */
public function testBasicExample()
{
    Cache::shouldReceive('get')
        ->with('key')
        ->andReturn('value');
    $this->visit('/cache')
        ->see('value');
}
```



#### How Facades Work

In a Laravel application, a facade is a class that provides access to an object from the container. The machinery that makes this work is in the `Facade` class. Laravel's facades. and any custom facades you create, will extend the base `Illuminate\Support\Facades\Facade` class.

The `Facade` base class makes use of the `__callStatic()` magic-method to defer calles from your facade to an object resolved from the container. In this example below, a call is made to the Laravel cache system. By glancing at the following code, one might assume that the static method `get` is being called on the `Cache` class:

```php
<?php
namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Support\Facades\Cache;

class UserController extends Controller
{
    /**
     * Show the profile for the given user.
     *
     * @param  int  $id
     * @return  Response
     */
    public function showProfile($id)
    {
        $user = Cache::get('user:' . $id);
        return view('profile', ['user' => $user]);
    }
}
```

Notice that near the top of the file we are "importing" the `Cache` facade. This facade serves as a proxy to accessing the underlying implementation of the `Illuminate\Contracts\Cache\Factory` interface. Any calls we make using the facade will be passed to the underlying instance of Laravel's cache service.

If we look at that `Illuminate\Support\Facades\Cache` class, you'll see that there is not static method `get`:

```php
class Cache extends Facade
{
    /**
     * Get the registered name of the component.
     *
     * @return  string
     */
    protected static function getFacadeAccessor()
    {
        return 'cache';
    }
}
```

Instead, the `Cache` facade extends the base `Facade` class and defines the method `getFacadeAccessor()`. This method's job is to return the name of a service container binding. When a user references any static method on the `Cache` facade, Laravel resolves the `cache` binding from the `service container` and runs the requested method(in this case, `get`) against that object.

#### Real-Time Facades

Using real-time facades, you may treat any class in your application as if it were a facade. To illustrate how this can be used, let's examine an alternative, For example, let's assume our `Podcast` model has a publish method. However, in order to publish the podcast, we need to inject a `Publisher` instance:

```php
<?php
namespace App;

use App\Contracts\Publisher;
use Illuminate\Database\Eloquent\Model;

class Podcast extends Model
{
    /**
     * Publish the podcast.
     *
     * @param  Publisher  $publisher
     * @return  void
     */
    public function publish(Publisher $publisher)
    {
        $this->update(['publishing' => now()]);
        $publisher->publish($this);
    }
}
```

Injecting a publisher implementation into the method allows us to easily test the method in isolation since we can mock the injected publisher. However, it requires us to always pass a publisher instance each time we call the `publish` method. Using real-time facades, we can maintain the same testability while not being required to explicitly pass a `Publisher` instance. To generate a real-time facade, prefix the namespace of the imported class with `Facades`:

```php
<?php
namespace App;

use Facades\App\Contracts\Publisher;
use Illuminate\Database\Eloquent\Model;

class Podcast extends Model
{
    /**
     * Publish the podcase.
     *
     * @return  void
     */
    public function publish()
    {
        $this->update(['publishing' => now()]);
        Publisher::publish($this);
    }
}
```

When the real-time facade is used, the publisher implementation will be resolved out of the service container using the portion of the interface or class name that appears after the `Facades` prefix. When testing, we can use Laravel's built-in facade testing helpers to mock this method call:

```php
<?php
namespace Tests\Feature;

use App\Podcase;
use Tests\TestCase;
use Facades\App\Contracts\Publisher;
use Illuminate\Foundation\Testing\RefreshDatabase;

class PodcastTest extends TestCase
{
    use RefreshDatabase;
    
    /**
     * A test example.
     *
     * @return  void
     */
    public function test_podcast_can_be_published()
    {
        $podcast = factory(Podcast::class)->create();
        Publisher::shouldReceive('publish')->once()->with($podcast);
        $podcast->publish();
    }
}
```



#### Facade Class Reference

Below we will find every facade and its underlying class. This is a useful tool for quickly digging into the API documentation for a given facade root. The `service container binding` key is also included where applicable.

| Facade              | Class                                           | Service Container Binding |
| ------------------- | ----------------------------------------------- | ------------------------- |
| App                 | Illuminate\Foundation\Application               | `app`                     |
| Artisan             | Illuminate\Contracts\Console\Kernel             | `artisan`                 |
| Auth                | Illuminate\Auth\AuthManager                     | `auth`                    |
| Auth(Instance)      | Illuminate\Contracts\Auth\Guard                 | `auth.driver`             |
| Blade               | Illuminate\View\\Compilers\BladeCompiler        | `blade.compiler`          |
| Broadcast           | Illuminate\Contracts\Broadcasting\Factory       |                           |
| Broadcast(Instance) | Illuminate\Contracts\Broadcasting\Broadcaster   |                           |
| Bus                 | Illuminate\Contracts\Bus\Dispatcher             |                           |
| Cache               | Illuminate\Cache\CacheManager                   | `cache`                   |
| Cache(Instance)     | Illuminate\Cache\Repository                     | `cache.store`             |
| Config              | Illuminate\Config\Repository                    | `config`                  |
| Cookie              | Illuminate\Cookie\CookieJar                     | `cookie`                  |
| Crypt               | Illuminate\Encryption\Encrypter                 | `encrypter`               |
| DB                  | Illuminate\Database\DatabaseManager             | `db`                      |
| DB(Instance)        | Illuminate\Database\Connection                  | `db.connection`           |
| Event               | Illuminate\Events\Dispatcher                    | `events`                  |
| File                | Illuminate\Filesystem\Filesystem                | `files`                   |
| Gate                | Illuminate\Contracts\Auth\Access\Gate           |                           |
| Hash                | Illuminate\Contracts\Hashing\Hasher             | `hash`                    |
| Lang                | Illuminate\Translation\Translator               | `translator`              |
| Log                 | Illuminate\Log\LogManager                       | `log`                     |
| Mail                | Illuminate\Mail\Mailer                          | `mailer`                  |
| Notification        | Illuminate\Notifications\ChannelManage          |                           |
| Password            | Illuminate\Auth\Passwords\PasswordBrokerManager | `auth.password`           |
| Password(Instance)  | Illuminate\Auth\Passwords\PasswordBroker        | `auth.password.broker`    |
| Queue               | Illuminate\Queue\QueueManager                   | `queue`                   |
| Queue(Instance)     | Illuminate\Contracts\Queue\Queue                | `queue.connection`        |
| Queue(Base Class)   | Illuminate\Queue\Queue                          |                           |
| Redirect            | Illuminate\Routing\Redirector                   | `redirect`                |
| Redis               | Illuminate\Redis\RedisManager                   | `redis`                   |
| Redis(Instance)     | Illuminate\Redis\Connections\Connection         | `redis.connection`        |
| Request             | Illuminate\Http\Request                         | `request`                 |
| Response            | Illuminate\Contracts\Routing\ResponseFactory    |                           |
| Response(Instance)  | Illuminate\Http\Response                        |                           |
| Route               | Illuminate\Routing\Router                       | `router`                  |
| Schema              | Illumiante\Database\Schema\Builder              |                           |
| Session             | Illuminate\Session\SessionManager               | `session`                 |
| Session(instance)   | Illuminate\Session\Store                        | `session.store`           |
| Storage             | Illuminate\Filesystem\FilesystemManager         | `filesystem`              |
| Storage(Instance)   | Illuminate\Contracts\Filesystem\Filesystem      | `filesystem.disk`         |
| URL                 | Illuminate\Routing\UrlGenerator                 | `url`                     |
| Validator           | Illuminate\Vailidation\Factory                  | `validator`               |
| Validator(Instance) | Illuminate\Validation\Validator                 |                           |
| View                | Illuminate\View\Factory                         | `view`                    |
| View(Instance)      | Illuminate\View\View                            |                           |



### 3.5 Contracts

#### Introduction

Laravel's Contracts are a set of interfaces that define the core services provided by the framework. For example, a `Illuminate\Contracts\Queue\Queue` contract defines the methods needed for queueing jobs, while the `Illuminate\Contracts\Mail\Mailer` contract defines the methods needed for sending email.

Each contract has a corresponding implementation provided by the framework. For example, Laravel provides a queue implementation with a variety of drivers, and a mailer implementation that is powered by `SwiftMailer`.

All of the Laravel contracts live in [their own github repository](https://github.com/illuminate/contracts). This provides a quick reference point for all available contracts, as well as a single, decoupled package that may be utilized by package developers.

##### Contracts & Facades

Laravel's facades and helper functions provide a simple way of utilizing Laravel's services without needing to type-hint and resolve contracts out of the service container. In most cases, which facade has an equivalent contract.

Unlike facades, which don't require you to require them in your class' constructor, contracts allow you to define explicit dependencies for your classes. Some developers prefer to explicitly define their dependencies in this way and therefore prefer to use contracts, while other developers enjoy the convenience of facades.

> Most applications will be fine regardless of whether you prefer facades or contracts. However, if you are building a package, you should strongly consider using contracts since they will be easier to test in a package context.



#### When To use Contracts

As discussed elsewhere, much of the decision to use contracts or facades will come down to personal taste and the taste of your development team. Both contracts and facades can be used to create robust, well-tested Laravel applications. As long as you are keeping your class' responsibilities focused, you will notice very few practical differences between using contracts and facades.

However, you may still have several questions regarding contracts. For example, why use interfaces at all? Isn't using interfaces more complicated? Let's distill the reason for using interfaces to the following headings: loose coupling and simplicity.

##### Loose Coupling

First, let's review some code that is tightly coupled to a cache implementation. Consider the following:

```php
<?php
namespace App\Orders;

class Repository
{
    /**
     * The cache instance.
     */
    protected $cache;
    
    /**
     * Create a new repository instance.
     *
     * @param  \SomePackage\Cache\Memcached  $cache
     * @return  void
     */
    public function __construct(\SomePackage\Cache\Memcached $cache)
    {
        $this->cache = $cache;
    }
    
    /**
     * Retrieve an Order by ID.
     *
     * @param  int  $id
     * @return  Order
     */
    public function find($id)
    {
        if($this->cache->has($id))
        {
            //
        }
    }
}
```

In this class, the code is tightly coupled to a given cache implementation. It is tightly coupled because we are depending on a concrete Cache class from a package vendor. If the API of that package changes our code must change as well.

Likewise, if we want to replace our underlying cache technology(Memcached) with another technology(Redis), we again will have to modify our repository. Our repository shouldn't have so much knowledge regarding who is providing them data or how they are providing it.

**Instead of this approach, we can improve our code by depending on a simple, vendro agnostic interface:**

```php
<?php
namespace App\Orders;

use Illuminate\Contracts\Cache\Repository as Cache;

class Repository
{
    /**
     * The cache instance.
     */
    protected $cache;
    
    /**
     * Create a new repository instance.
     *
     * @param  Cache  $cache
     * @return  void
     */
    public function __construct(Cache $cache)
    {
        $this->cache = $cache;
    }
}
```

Now the code isn't coupled to any specific vendor, or even Laravel. Since the contracts package contains no implementation and no dependencies, you may easily write an alternative implementation of any given contract, allowing you to replace your cache implementation without modifying any of your cache consuming code.

##### Simplicity

When all of Laravel's services are neatly defined within simple interfaces, it's very easy to determine the functionality offered by a given service. **The contracts serve as succinct documentation to the framework's features.**

In addition, when you depend on simple interfaces, your code is easier to understand and maintain. Rather than tracking down which methods are available to you within a large, complicated class, you can refer to a simple, clean interface.

#### How to use Contracts

So, how do you get an implementation of a contract? It's actually quite simple.

Many types if classes in Laravel are resolved through the `service container`, including controllers, event listeners, middleware, queued jobs, and even route Closures. So, to get an implementation of a contract, you can just "type-hint" the interface in the constructor of the class being resolved.

For example, take a look at this event listener:

```php
<?php
namespace App\Listeners;

use App\User;
use App\Events\OrderWasPlaced;
use Illuminate\Contracts\Redis\Factory;

class CacheOrderInformation
{
    /**
     * The Redis factory implementation
     */
    protected $redis;
    
    /**
     * Create a new event handler instance.
     *
     * @param  Factory  $redis
     * @return  void
     */
    public function __construct(Factory $redis)
    {
        $this->redis = $redis;
    }
    
    /**
     * Handle the event.
     *
     * @param  OrderWasPlaced  $event
     * @return  void
     */
    public function handle(OrderWasPlaced $event)
    {
        //
    }
}
```

When the event listener is resolved, the service container will read the type-hints on the constructor of the class, and inject the appropriate value. 

#### Contract Reference

This table provides a quick reference to all of the Laravel contracts and their equivalent facades:

| Contract                                              | References Facade         |
| ----------------------------------------------------- | ------------------------- |
| Illuminate\Contracts\Auth\Access\Authorizable         |                           |
| Illuminate\Contracts\Auth\Access\Gate                 | `Gate`                    |
| Illuminate\Contracts\Auth\Authenticatable             |                           |
| Illuminate\Contracts\Auth\CanResetPassword            |                           |
| Illuminate\Contracts\Auth\Factory                     | `Auth`                    |
| Illuminate\Contracts\Auth\Guard                       | `Auth::guard()`           |
| Illuminate\Contracts\Auth\PasswordBroker              | `Password::broker()`      |
| Illuminate\Contracts\Auth\PasswordBrokerFactory       | `password`                |
| Illuminate\Contracts\Auth\StatefulGuard               |                           |
| Illuminate\Contracts\Auth\SupportsBasicAuth           |                           |
| Illuminate\Contracts\Auth\UserProvider                |                           |
| Illuminate\Contracts\Bus\Dispatcher                   | `Bus`                     |
| Illuminate\Contracts\Bus\QueueingDispatcher           | `Bus::dispatchToQueue()`  |
| Illuminate\Contracts\Broadcasting\Factory             | `Broadcast`               |
| Illuminate\Contracts\Broadcasting\Broadcaster         | `Broadcast::connection()` |
| Illuminate\Contracts\Broadcasting\ShouldBroadcast     |                           |
| Illuminate\Contracts\Broadcasting\ShouldBroadcastNow  |                           |
| Illuminate\Contracts\Cache\Factory                    | `Cache`                   |
| Illuminate\Contracts\Cache\Lock                       |                           |
| Illuminate\Contracts\Cache\LockProvider               |                           |
| Illuminate\Contracts\Cache\Repository                 | `Cache::driver()`         |
| Illuminate\Contracts\Cache\Store                      |                           |
| Illuminate\Contracts\Config\Repository                | `Config`                  |
| Illuminate\Contracts\Console\Application              |                           |
| Illuminate\Contracts\Console\Kernel                   | `Artisan`                 |
| Illuminate\Contracts\Container\Container              | `App`                     |
| Illuminate\Contracts\Cookie\Factory                   | `Cookie`                  |
| Illuminate\Contracts\Cookie\QueueingFactory           | `Cookie::queue()`         |
| Illuminate\Contracts\Database\ModelIdentifier         |                           |
| Illuminate\Contracts\Debug\ExceptionHandler           |                           |
| Illuminate\Contracts\Encryption\Encrypter             | `Crypt`                   |
| Illuminate\Contracts\Events\Dispatcher                | `Event`                   |
| Illuminate\Contracts\Filesystem\Cloud                 | `Storage::cloud()`        |
| Illuminate\Contracts\Filesystem\Factory               | `Storage`                 |
| Illuminate\Contracts\Filesystem\Filesystem            | `Storage::disk()`         |
| Illuminate\Contracts\Foundation\Application           | `App`                     |
| Illuminate\Contracts\Hashing\Hasher                   | `Hash`                    |
| Illuminate\Contracts\Http\Kernel                      |                           |
| Illuminate\Contracts\Mail\MailQueue                   | `Mail::queue()`           |
| Illuminate\Contracts\Mail\Mailable                    |                           |
| Illuminate\Contracts\Mail\Mailer                      | `Mail`                    |
| Illuminate\Contracts\Notifications\Dispatcher         | `Notification`            |
| Illuminate\Contracts\Notifications\Factory            | `Notification`            |
| Illuminate\Contracts\Pagination\LengthAwarePaginator  |                           |
| Illuminate\Contracts\Pagination\Paginator             |                           |
| Illuminate\Contracts\Pipeline\Hub                     |                           |
| Illuminate\Contracts\Pipeline\Pipeline                |                           |
| Illuminate\Contracts\Queue\EntityResolver             |                           |
| Illuminate\Contracts\Queue\Factory                    | `Queue`                   |
| Illuminate\Contracts\Queue\Job                        |                           |
| Illuminate\Contracts\Queue\Monitor                    | `Queue`                   |
| Illuminate\Contracts\Queue\Queue                      | `Queue::connection()`     |
| Illuminate\Contracts\Queue\QueueableCollection        |                           |
| Illuminate\Contracts\Queue\QueueableEntity            |                           |
| Illuminate\Contracts\Queue\ShouldQueue                |                           |
| Illuminate\Contracts\Redis\Factory                    | `Redis`                   |
| Illuminate\Contracts\Routing\BindingRegistrar         | `Route`                   |
| Illuminate\Contracts\Routing\Registrar                | `Route`                   |
| Illuminate\Contracts\Routing\ResponseFactory          | `Response`                |
| Illuminate\Contracts\Routing\UrlGenerator             | `URL`                     |
| Illuminate\Contracts\Routing\UrlRoutable              |                           |
| Illuminate\Contracts\Session\Session                  | `Session::driver()`       |
| Illuminate\Contracts\Support\Arrayable                |                           |
| Illuminate\Contracts\Support\Htmlable                 |                           |
| Illuminate\Contracts\Support\Jsonable                 |                           |
| Illuminate\Contracts\Support\MessageBag               |                           |
| Illuminate\Contracts\Support\MessageProvider          |                           |
| Illuminate\Contracts\Support\Renderable               |                           |
| Illuminate\Contracts\Support\Responsable              |                           |
| Illuminate\Contracts\Translation\Loader               |                           |
| Illuminate\Contracts\Translation\Translator           | `Lang`                    |
| Illuminate\Contracts\Validation\Factory               | `Validator`               |
| Illuminate\Contracts\Validation\ImplicitRule          |                           |
| Illuminate\Contracts\Validation\Rule                  |                           |
| Illuminate\Contracts\Validation\ValidatesWhenResolved |                           |
| Illuminate\Contracts\Validation\Validator             | `Validator::make()`       |
| Illuminate\Contracts\View\Engine                      |                           |
| Illuminate\Contracts\View\Factory                     | `View`                    |
| Illuminate\Contracts\View\View                        | `View::make()`            |



## 4.The Basics

### 4.1 Routing

#### Basic Routing

The most basic Laravel routes accept a URL and a `Closure`. providing a very simple and expressive method of defining routes:

```php
Route::get('foo', function () {
    return 'Hello world';
});
```

###### The default route files

All Laravel routes are defined in your route files, which are located in the `routes` directory. These files are automatically loaded by the framework. The `routes/web.php` file defines routes that are for your web interface. These routes are assigned the `web` middleware group, which provides features like session state and CSRF protection. The routes in `route/api.php` are stateless and are assigned the `api` middleware group.

For most applications, you will begin by defining routes in your `routes/web.php` file. The routes defined in `routes/web.php` may be accessed by entering the defined route's URL in your browser. For example, you may access the following route by navigating to `http://yourapp.test/user` in your browser:

```php
Route::get('/user', 'UserController@index');
```

Routes defined in the `routes/api.php` file are nested within a route group by the `RouteServiceProvider`. Within this group, the `/api` URL prefix is automatically applied so you don't need to manually apply it to every route in the file. You may modify the prefix and other route group options by modifying your `RouteServiceProvider` class.

###### Available Router Methods

The router allows you to register routes that respond to any HTTP verb:

```php
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
```

Sometimes you may need to register a route that responds to multiple HTTP verbs. You may do so using the `match` method. Or, you may even register a route that responds to all HTTP verbs using the `any` method:

```php
Route::match(['get', 'post'], '/', function () {
   	// 
});
Route::any('foo', function () {
   	// 
});
```

###### CSRF Protection

Any HTML forms pointing to `POST`, `PUT`, or `DELETE` routes that are defined in the `web` routes file should include a CSRF token field. Otherwise, the request will be rejected. You can read more about CSRF protection in the `The Basics/CSRF Protection`:

```php
<form method="POST" action="/profile">
	@csrf
    ...
</form>
```

##### Redirect Routes

If you are defining a route that redirects to another URI, you may use the `Route::redirect` method. This method provides a convenient shortcut so that you don't have to define a full route or container for performing a simple redirect:

```php
Route::redirect('/url1', '/url2');
```

By default, `Route::redirect` returns a `302` status code. You may customize the status code using the optional third parameter:

```php
Route::redirect('/url1', '/url2', 301);
```

You may use the `Route::permanentRedirect` method to return a `301` status code:

```php
Route::permanentRedirect('/url1', '/url2');
```

##### View Routes

If your route only needs to return a view, you may use the `Route::view` method. Like the `redirect` method, this method, provides a simple shortcut so that you don't have to define a full route or controller. The `view` method accepts to a URI as its first argument and a view name as its second argument. In addition, you may provide an array of data to pass to the view as an optional third argument:

```php
Route::view('/welcome', 'welcome');
Route::view('/welcome', 'welcome', ['name' => 'Bob']);
```

#### Route Parameters

##### Required Parameters

Sometimes you will need to capture segments of the URI within your route. For example, you may need to capture a user's ID from the URL. You may do so by defining route parameters.

```php
Route::get('user/{id}', function ($id) {
    return 'User ' . $id;
});
```

You may define as many route parameters as required by your route:

```php
Route::get('posts/{post}/comments/{comment}', function ($postId, $commentId) {
    //
});
```

Route parameters are always encased within `{}` braces and should consist of alphabetic characters, and may not contain a `-` character. Instead of using the `-` character, use an underscore `(-)`. Route parameters are injected into route callbacks / controllers based on their order - the names of the callback / controller arguments don't matter.

##### Optional Parameters

Occasionally you may need to specify a route parameter, but make the presence of that route parameter optional. You may do so by placing a `?` mark after the parameter name. Make sure to give the route's corresponding variable a default value:

```php
Route::get('user/{name?}', function ($name = null) {
    return $name;
});
Route::get('user/{name?}', function ($name = null) {
    return $name;
});
```

##### Regular Expression Constraints

You may constrain the format of your route parameters using the `where` method on a route instance. The `where` method accepts the name of the parameter and a regular expression defining how the parameter should be constrained:

```php
Route::get('user/{name}', function ($name) {
    //
})->where('name', '[A-Za-z]+');
Route::get('user/{id}', function ($id) {
    //
})->where('id', '[0-9]+');
Route::get('user/{id}/{name}', function ($id, $name) {
    //
})->where(['id' => '[0-9]+', 'name' => '[a-z]+']);
```

###### Global Constraints

If you would like a route parameter to always be constrained by a given regular expression, you may use the `pattern` method. You should define these patterns in the `boot` method of your `RouteServiceProvider`:

```php
/**
 * Define your route model bindings, pattern filters, etc.
 *
 * @return  void
 */
public function boot()
{
    Route::pattern('id', '[0-9]+');
    parent::boot();
}
```

Once the pattern has been defined, it's automatically applied to all routes using that parameter name:

```php
Route::get('user/{id}', function ($id) {
    // only executed if {id} is numeric
});
```

###### Encoded Forward Slashes

The Laravel routing component allows all characters except `/.` You must explicitly allow `/` to be part of your placeholder using a `where` condition regular expression:

```php
Route::get('search/{search}', function ($search) {
    return $search;
})->where('search', '.*');
```

> Encoded forward slashes are only supported within the last route segment.

#### Named Routes

Named routes allow the convenient generation of URLs or redirects for specific rotues. You may specify a name for a route by chaining the `name` method onto the route definition:

```php
Route::get('user/profile', function () {
    //
})->name('profile');
```

You may also specify route names for controller actions:

```php
Route::get('user/profile', 'UserProfileController@show')->name('profile');
```

###### Generating URLs To Named Routes

Once you have assigned a name to a given route, you may use the route's name when generating URLs or redirects via the global `route` function:

```php
// Generating URLs...
$url = route('profile');
// Generating Redirects...
return redirect()->route('profile');
```

If the named route defines parameters, you may pass the parameters as the second argument to the `route` function. The given parameters will automatically be inserted into the URL in their correct positions:

```php
Route::get('user/{id}/profile', function ($id) {
    //
})->name('profile');
$url = route('profile', ['id' => 1]);
```

###### Inspecting The current route

If you would like to determine if the current request was routed to a given named route, you may use the `named` method on a Route instance. For example, you may check the current route name from a route middleware:

```php
/**
 * Handle an incoming request.
 *
 * @param  \Illuminate\Http\Request  $request
 * @param  \Closure  $next
 * @return  mixed
 */
public function handle($request, Closure $next)
{
    if($request->route()->named('profile')) {
        //
    }
    return $next($request);
}
```

#### Route Groups

Route groups allow you to share route attributes, such as middleware or namespaces, across a large number of routes without needing to define those attributes on each individual route. Shared attributes are specified in an array format as the first parameter to the `Route::group` method.

Nested groups attempt to intelligently "merge" attributes with their parent group. Middleware and `where` conditions are merged while names, namespaces, and prefixes are appended. Namespace delimiters and slashes in URL prefixes are automatically added where appropriate.

##### Middleware

To assign middleware to all routes within a group, you may use the `middleware` method before defining the group. Middleware are executed in the order they are listed in the array:

```php
Route::middleware(['first', 'second'])->group(function () {
    Route::get('/', function () {
        // Uses first & second Middleware
    });
    Route::get('user/profile', function () {
        // Uses first & second Middleware
    });
});
```

##### Namespaces

Another common use-case for route groups is assigning the same PHP namespace to a group of controllers using the `namespace` method:

```php
Route::namespace('Admin')->group(function () {
    // Controllers Within The "App\Http\Controllers\Admin" Namespace
});
```

Remember, by default, the `RouteServiceProvider` includes your route files within a namespace group, allowing you to register controller routes without specifying the full `App\Http\Controllers` namespace prefix. So, you only need to specify the portion of the namespace that comes after the base `App\Http\Controllers` namespace.

##### Sub-Domain Routing

Route groups may also be used to handle sub-domain routing. Sub-domains may be assigned route parameters just like route URIs, allowing you to capture a portion of the sub-domain for usage in your route or controller. The sub-domain may be specified by calling the `domain` mpethod before defining the group:

```php
Route::domain('{account}.myapp.com')->group(function () {
    Route::get('user/{id}', function ($account, $id) {
        //
    });
});
```

##### Route Prefixes

The `prefix` method may be used to prefix each route in the group with a given URL. For example, you may want to prefix all route URLs within the group with `admin`:

```php
Route::prefix('admin')->group(function () {
    Route::get('users', function () {
        // Matches The "/admin/users" URL
    });
});
```

##### Route Name Prefixes

The `name` method may be used to prefix each rotue name in the group with a given string. For example, you may want to prefix all of the grouped route's names with `admin`. The given string is prefixed to the route name exactly as it's specified, so we will be sure to provide the trailing `.` character in the prefix:

```php
Route::name('admin.')->group(function () {
    Route::get('users', function () {
        // Route assigned name "admin.users"...
    });
});
```

#### Route Model Binding

When injecting a model ID to a route or controller action, you will often query to retrieve the model that corresponds to that ID. Laravel route model binding provides a convenient way to automatically inject the model instances directly into your routes. For example, instead of injecting a user's ID, you can inject the entire `User` model instance that matches the given ID.

##### Implicit Binding

Laravel automatically resolves Eloquent models defined in route or controller actions whose type-hinted variable names match a route segment name. For example:

```php
Route::get('api/users/{user}', function (App\User $user) {
    return $user->email;
});
```

Since the `$user` variable is type-hinted as the `App\User` Eloquent model and the variable name matches the `{user}` URI segment, Laravel will automatically inject the model instance that has an ID matching the corresponding value from the request URI. If a matching model instance is not found in the database, a 404 HTTP response will automatically be generated.

###### Customizing the Key Name

If you would like modeling binding to use a database column other than `id` when retrieving a given model class, you may override the `getRouteKeyName` method on the Eloquent model:

```php
/**
 * Get the route key for the model.
 *
 * @return  string
 */
public function getRouteKeyName()
{
    return 'slug';
}
```

##### Explicit Binding

To register an explicit binding, use the router's `model` method to specify the class for a given parameter. You should define your explicit model bindings in the `boot` method of the `RouteServiceProvider` class:

```php
public function boot()
{
	parent::boot();
    Route::model('user', App\User::class);
}
```

Next, define a route that contains a `{user}` parameter:

```php
Route::get('profile/{user}', function (App\User $user) {
    //
});
```

Since we have bound all `{user}` parameters to the `App\User` model, a `User` instance will be injected into the route. So, for example, a request to `profile/1` will inject the `User` instance from the database which has an ID of `1`.

If a matching model instance is not found in the database, a 404 HTTP response will be automatically generated.

###### Customizing the Resolution Logic

If you wish to use your own resolution logic, you may use the `Route::bind` method. The `Closure` you pass to the `bind` method will receive the value of the URI segment and should return the instance of the class that should be injected into the route:

```php
/**
 * Bootstrap any application services
 *
 * @return  void
 */
public function boot()
{
    parent::boot();
    
    Route::bind('user', function ($value) {
        return App\User::where('name', $value)->first() ?? abort(404);
    });
}
```

Alternatively, you may override the `resolveRouteBinding` method on your Eloquent model. This method will receive the value of the URI segment and should return the instance of the class that should be injected into the route:

```php
/**
 * Retrieve the model for a bound value.
 *
 * @param  mixed  $value
 * @return  \Illuminate\Database\Eloquent\Model|null
 */
public function resolveRouteBinding($value)
{
    return $this->where('name', $value)->first() ?? abort(404);
}
```

#### Fallback Routes

Using the `Route::fallback` method, you may define a route that will be executed when no other route matches the incoming request. Typically, unhandled requests will automatically render a "404" page via your application's exception handler. However, since you may define the `fallback` route within your `routes/web.php` file, all middleware in the `web` middleware group will apply to the route. You are free to add additional middleware to this route as needed:

```php
Route::fallback(function () {
    //
});
```

> The fallback route should always be the last route registered by your application.

#### Rate Limiting

Laravel includes a `middleware` to rate limit access to routes within your application. To get started, assign the `throttle` middleware to a route or a group of routes. The `throttle` middleware accepts two parameters that determine the maximum number of requests that can be made in a given number of minutes. For example, let's specify that an authenticated user may access the following group of routes 60 times per minute:

```php
Route::middleware('auth:api', 'throttle:60,1')->group(function （） {
    Route::get('/user', function () {
        //
    });
});
```

###### Dynamic Rate Limiting

You may specify a dynamic request maximum based on an attribute of the authenticated `User` model. For example, if your `User` model contains a `rate_limit` attribute, you may pass the name of the attribute to the `throttle` middleware so that it's used to calculate the maximum request count:

```php
Route::middleware('auth:api', 'throttle:rate_limit,1')->group(function () {
    Route::get('/user', function () {
        //
    });
});
```

#### From Method Spoofing

HTML forms don't support `PUT`, `PATCH`, `DELETE` actions. So, when defining `PUT`, `PATCH` or `DELETE` routes that are called from an HTML form, you will need to add a hidden `_method` field to the form. The value sent with the `_method` field will be used as the HTTP request method:

```html
<form action="/foo/bar" method="POST">
    <input type="hidden" name="_method" value="PUT"/>
    <input type="hidden" name="_token" value="{{ csrf_token() }}"/>
</form>
```

You may use the `@method` Blade directive to generate the `_method` input:

```html
<form action="/foo/bar" method="POST">
    @method('PUT')
    @csrf
</form>
```

#### Accessing The Current Route

You may use the `current`, `currentRouteName`,  and `current RouteAction` methods on the `Route` facade to access information about the route handling the incoming request:

```php
$route = Route::current();
$name = Route::currentRouteName();
$action = Route::currentRouteAction();
```

Refer to the API documentation for both the `underlying class of the Route facade` and `Route instance` to review all accessible methods.

### 4.2 Middleware

#### Introduction

Middleware provide a convenient mechanism for filtering HTTP requests entering your application. For example, Laravel includes a middleware that verifies the user of your application is authenticated. If the user isn;t authenticated, the middleware will redirect the user to the login screen. However, if the user is authenticated, the middleware will allow the request to proceed further into the application.

Additional middleware can be written to perform a variety of tasks besides authentication. A CORS middleware might be responsible for adding the proper headers to all responses leaving your application. A logging middleware might log all incoming requests to your application.

There are several middleware included in the Laravel framework, including middleware for authentication and CSRF protection. All of these middleware are located in the `app/Http/Middleware` directory.

#### Defining Middleware

To create a new middleware, use the `make:middleware` Artisan command:

```shell
php artisan make:middleware CheckAge
```

This command will place a new `CheckAge` class within your `app/Http/Middleware` directory. In this middleware, we will only allow access to the route if the supplied `age` is greater than 200. Otherwise, we will redirect the users back to the `home` URI:

```php
<?php
    
namespace App\Http\Middleware;

use Closure;

class CheckAge
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return  mixed
     */
    public function handle($request, Closure $next)
    {
        if($request->age <= 200) {
            return redirect('home');
        }
        return $next($request);
    }
}
```

As we can see, if the given `age` is less than or equal to `200`, the mddleware will return an HTTP redirect to the client; otherwise, the request will be passed further into the application. To pass the request deeper into the application(allowing the middleware to "pass"), call the `$next` callback with the `$request`.

It's best to envision middleware as a series of "layer" HTTP requests must pass through before they hit your application. Each layer can examine the request and even reject it entirely.

> All middleware are resolved via the `service container`, so you may type-hint any dependencies you need within a middleware's constructor.

##### Before & After Middleware

Whether a middleware runs before or after a request depends on the middleware itself. For example, the following middleware would perform some task before the request is handled by the application:

```php
<?php
namespace App\Http\Middleware;

use Closure;

class BeforeMiddleware
{
    public function handle($request, Closure $next)
    {
        // Perform action
        return $next($request);
    }
}
```

However, this middleware would perform its task after the request is handled by the application:

```php
<?php
    
namespace App\Http\Middleware;

use Closure;

class AfterMiddleware
{
    public function handle($request, Closure $next)
    {
       	$response = $next($request);
        
        // Perform action
        
        return $response;
    }
}
    
```

#### Registering Middleware

##### Global Middleware

If you want a middleware to run during every HTTP request to your application, list the middleware class in the `$middleware` property of your `app/Http/Kernel.php` class.

##### Assigning Middleware To Routes

If you would like to assign middleware to specific routes, you should first assign the middleware a key in your `app/Http/Kernel.php` file. By default, the `$routeMiddleware` property of this class contains entries for the middleware included with Laravel. To add your own, append it to this list and assign it a key of your choosing:

```php
// Within App\Http\Kernel Class...
protected $routeMiddleware = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
    'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
    'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
    'can' => \Illuminate\Auth\Middleware\Authorize::class,
    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
    'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
];
```

Once the middleware has been defined in the HTTP kernel, you may use the `middleware` method to assign middleware to a route:

```php
Route::get('admin/profile', function () {
   	// 
})->middleware('auth');
```

You may also assign multiple middleware to the route:

```php
Route::get('/', function () {
    //
})->middleware('first', 'second');
```

When assigning middleware, you may also pass the fully qualified class name:

```php
use App\Http\Middleware\CheckAge;

Route::get('admin/profile', function () {
   	// 
})->middleware('CheckAge::class');
```

##### Middleware Groups

Sometimes you may want to group several middleware under a single key to make them easier to assign to routes. You may do this using the `$middlewareGroups` property of your HTTP kernel.

Out of the box, Laravel comes with `web` and `api` middleware groups that contain common middleware you may want to apply to your web UI and API routes:

```php
/**
 * The application's route middleware groups.
 *
 * @var  array
 */
protected $middlewareGroups = [
    'web' => [
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\VerifyCsrfToken::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],
    'api' => [
        'throttle:60,1',
        'auth:api',
    ],
];
```

Middleware groups may be assigned to routes and controller actions using the same syntax as individual middleware. Again, middleware groups make it more convenient to assign many middleware to a route at once:

```php
Route::get('/', function () {
    //
})->middleware('web');
Route::group(['middleware' => ['web']], function () {
    //
});
```

> Out of the box, the `web` middleware group is automatically applied to your `routes/web.php` file by the `RouteServiceProvider`.

##### Sorting Middleware

Rarely, you may need your middleware to execute in a specific order but not have control over their order when they are assigned to the route. In this case, you may specify your middleware priority using the `$middlewarePriority` property of your `app/Http/Kernel.php` file:

```php
/**
 * The priority-sorted list of middleware.
 *
 * This forces non-global middleware to always be in the given order.
 *
 * @var  array
 */
protected $middlewarePriority = [
    \Illuminate\Session\Middleware\StartSession::class,
    \Illuminate\View\Middleware\ShareErrorsFromSession::class,
    \App\Http\Middleware\Authenticate::class,
    \Illuminate\Session\Middleware\AuthenticateSession::class,
    \Illuminate\Routing\Middleware\SubstituteBindings::class,
    \Illuminate\Auth\Middleware\Authorize::class,
];
```



#### Middleware Parameters

Middleware can also receive additional parameters. For example, if your application needs to verify that the authenticated user has a given "role" before performing a given action, you could create a `CheckRole` middleware that receives a role name as an additional argument.

Additional middleware parameters will be passed to the middleware after the `$next` argument:

```php
<?php

namespace App\Http\Middleware;

use Closure;

class CheckRole
{
    /**
     * Handle the incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @param  string  $role
     * @return  mixed
     */
    public function handle($request, Closure $next, $role)
    {
        if(!$request->user()->hasRole($role)) {
            // Redirect..
        }
        return $next($request);
    }
}
```

Middleware parameters may be specified when defining the route by separating the middleware name and parameters with a `:`. Multiple parameters should be delimited by commas:

```php
Route::put('post/{id}', function ($id) {
    //
})->middleware('role:editor');
```



#### Terminable Middleware

Sometimes a middleware may need to do some work after the HTTP response has been prepared. For example, the "session" middleware included with Laravel writes the session data to storage after the response has been fully prepared. If you define a `terminate` method on your middleware, it will automatically be called after the response is ready to be sent to the browser.

```php
<?php

namespace Illuminate\Session\Middleware;

use Closure;

class StartSession
{
    public function handle($request, Closure $next)
    {
        return $next($request);
    }
    
    puublic function terminate($request, $response)
    {
        // Store the session data...
    }
}
```

The `terminate` method should receive both the request and the response. Once you have defined a terminable middleware, you should add it to the list of route or global middleware in the `app/Http/Kernel.php` file.

When calling the `terminate` method on your middleware, Laravel will resolve a fresh instance of the middleware from the `service container`. If you would like to use the same middleware instance when the `handle` and `terminate` methods are called, register the middleware with the container using the container's `singleton` method.

### 4.3 CSFR Protection

#### Introduction

Laravel makes it easy to protect your application from `cross-site request forgery`(CSRF) attacks. Cross-site request Forgeries are a type pf malicious exploit whereby unauthorized commands are performed on behalf of an authenticated user.

Laravel automatically generates a CSRF "token" for each active user session managed by the application. This token is used to verify that the authenticated user is the one actually making the requests to the application.

Anytime you define a HTML form in your application, you should include a hidden CSRF token field in the form so that the CSRF protection middleware can validate the request. You may use the `@csrf` Blade directive to generate the token field:

```html
<form method="POST" action="/profile">
    @csrf
    ...
</form>
```

The `verifyCsrfToken`  **middleware**, which is included in the `web` middleware group, will automatically verify that the token in the request input matches the token stored in the session.

###### CSRF Token & Javascript

When building JavaScript driven applications, it is convenient to have your JavaScript HTTP library automatically attach the CSRF token to every outgoing request. By default, the `resources/js/bootstrap.js` file registers the value fo the `csrf-token` meta tag with the Axios HTTP library. If you aren't using this library, you will need to manually configure this behavior for your application.

#### Excluding URIs From CSRF Protection

Sometimes you may wish to exclude a set of URIs from CSRF protection. For example, if you are using `Stripe(https://stripe.com)` to process payments and are utilizing their webhook system, you will need to exclude your Stripe webhook handler route from CSRF protection since Stripe will not know what CSRF token to send to your routes.

Typically, you should place these kinds of routes outside of the `web` middleware group that the `RouteServiceProvider` applies to all routes in the `routes/web.php` file. However, you may also exclude the routes by adding their URIs to the `$except` property of the `VerifyCsrfToken` middleware:

```php
<?php
    
namespace App\Http\Middleware;

use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as Middleware;

class VerifyCsrfToken extends Middleware
{
    /**
     * The URIs that should be excluded from CSRF verification.
     *
     * @var array
     */
    protected $except = [
        'stripe/*',
        'http://example.com/foo/bar',
        'http://example.com/foo/*',
    ];
}
```



#### X-CSRF-TOKEN

In addition to checking for the CSRF token as a POST parameter, the `VerifyCsrfToken` middleware will also check for the `X-CSRF-TOKEN` request header. You could, for example, store the token in a HTML `meta` tag:

```html
<meta name="csrf-token" content="{{ csrf_token() }}">
```

Then, once you have created the `meta` tag, you can instruct a library like jQuery to automatically add the token to all request headers. This provides simple, convenient CSRF protection for your AJAX based applications:

```javascript
$.ajaxSetup({
    headers: {
        'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
    }
});
```

> By default, the `resources/js/bootstrap.js` file registers the value of the `csrf-token` meta tag with the Axios HTTP library. If you are not using this library, you will need to manually configure this behavior for your application.

#### X-XSRF-Token

Laravel stores the current CSRF token in a `XSRF-TOKEN` cookie that is included with each response generated  by the framework.  You can use the cookie value to set the `X-XSRF-TOKEN` request header.

This cookie is primarily sent as a convenience since some JavaScript frameworks and libraries, like Angular and Axios, automatically place its value in the `X-XSRF-TOKEN` header.

### 4.4 Controllers

#### Introduction

Instead of defining all of your request handling logic as Closures in route files, you may wish to organize this behavior using Controller classes. Controllers can group related request handling logic into a single class. Controllers are stored in the `app/Http\Controllers` directory.

#### Basic Controllers

##### Defining Controllers

The following example is an example of a basic controller class. Note that the controller extends the base controller class included with Laravel. The base class provides a few convenience methods such as the `middleware` method, which may be used to attach middleware to controller actions:

```php
<?php

namespace App\Http\Controllers;

use App\User;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    /**
     * Show the profile for the given user.
     *
     * @param  int  $id
     * @return  View
     */
    public function show($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}
```

You can define a route to this controller action like so:

```php
Route::get('user/{id}', 'UserController@show');
```

Now, when a request matches the specified route URI, the `show` method on the `UserController` class will be executed. The route parameters will also be passed to the method.

> Controllers **aren't** **required** to extend a base class. However, you won't have access to convenience features such as the `middleware`, `validate`, and `dispatch` methods.

##### Controllers & Namespaces

It's very important to note that we didn't need to specify the full controller namespace when defining the controller route. Since the `RouteServiceProvider` loads your route files within a route group that contains the namespace, we only specified the portion of the class name that comes after the `App/Http/Controllers` portion of the namespace.

If you choose to nest your controllers deeper into the `App\Http\Controllers` directory, use the specific class name relative to the `App\Http\Controllers` root namespace. So, if your full controller class is `App\Http\Controllers\Photos\AdminController`, you should register routes to the controller like so:

```php
Route::get('foo', 'Photos\AdminController@action');
```



##### Single Action Controllers

If you would like to define a controller that only handles a single action, you may place a single `__invoke` method on the controller:

```php
<?php

namespace App\Http\Controllers;

use App\User;
use App\Http\Controllers\Controller;

class ShowProfile extends Controller
{
    /**
     * Show the prifile for the given user.
     *
     * @param  int  $id
     * @return  View
     */
    pulic function __invoke($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}
```

When registering routes for single action controllers, you don't need to specify a method:

```php
Route::get('user/{id}', 'ShowProfile');
```

You may generate an invokable controller by using the `--invokable` option of the `make:controller` Artisan command:

```php
php artisan make:controller ShowProfile --invokable
```

#### Controller Middleware

Middleware may be assigned to the controller's routes in your route files:

```php
Route::get('profile', 'UserController@show')->middleware('auth');
```

However, it's more convenient to specify middleware within your controller's constructor. Using the `middleware` method from your controller's constructor, you may easily assign middleware to the controller's action. You may even restrict the middleware to only certain methods on the controller class:

```php
class UserController extends Controller
{
    /**
     * Instantiate a new controller instance.
     *
     * @return  void
     */
    public function __construct()
    {
        $this->middleware('auth');
        $this->middleware('log')->only('index');
        $this->middleware('subscribed')->except('store');
    }
}
```

Controllers also allow you to register middleware using a Closure. This provides a convenient way to define a middleware for a single controller without defining an entire middleware class:

```php
$this->middleware(function ($request, $next) {
    // ...
    return $next($request);
});
```

> You may assign middleware to a subset of controller actions; however, it may indicate your controller is growing too large. Instead, considering breaking your controller into multiple, smaller controllers.

#### Resource Controller

Laravel resource routing assigns the typical "CRUD" routes to a controller with a single line of code. For example, you may wish to create a controller that handles all HTTP request for "photos" stored by your application. Using the `make:controller` Artisan command, we can quickly create such a controller:

```php
php artisan make:controller PhotoController --resource
```

This command will generate a controller at `app/Http/Controllers/PhotoController.php`. The controller will contain a method for each of the available resource operations.

Next, you may register a resourceful route to the controller:

```php
Route::resource('photos', 'PhotoController');
```

This single route declaration creates multiple routes to handle a variety of actions on the resource. The generated controller will already have methods stubbed for each of these actions, including notes informing you of the HTTP verbs and URIs they handle.

You may register many resource controllers at once by passing an array to the `resources` method:

```php
Route::resources([
    'photos' => 'PhotoController',
    'posts' => 'PostController',
]);
```

Actions handled by resource controller

| Verb      | URI                  | Action  | Route Name     |
| --------- | -------------------- | ------- | -------------- |
| GET       | /photos              | index   | photos.index   |
| GET       | /photos/create       | create  | photos.create  |
| POST      | /photos              | store   | photos.store   |
| GET       | /photos/{photo}      | show    | photos.show    |
| GET       | /photos/{photo}/edit | edit    | photos.edit    |
| PUT/PATCH | /photos/{photo}      | update  | photos.update  |
| DELETE    | /photos/{photo}      | destroy | photos.destroy |

###### Specifying the Resource Model

If we are using route model binding and would like the resource controller's methods to type-hint a model instance, we may use the `--model` option when generating the controller:

```shell
php artisan make:controller PhotoController --resource --model=Photo
```

###### Spoofing Form Methods

Since HTML forms can't make `PUT`, `PATCH` or `DELETE` requests, you will need to add a hidden `_method` field to spoof these HTTP verbs. The `@method` Blade directive can create this for us:

```html
<form action="/foo/bar" method="POST">
    @method('PUT')
</form>
```

##### Partial Resource Routes

When declaring a resource route, you may specify a subset of actions the controller should handle instead of the full set of default actions:

```php
Route::resource('photos', 'PhotoController')->only([
    'index', 'show'
]);
Route::resource('photos', 'PhotoController')->except([
    'create', 'store', 'update', 'destroy'
]);
```

###### API Resource Routes

When declaring resource routes that will be consumed by APIs, you will commonly want to exclude routes that preset HTML templates such as `create` and `edit`. For convenience, you may use the `apiResource` method to automatically exclude these two routes:

```php
Route::apiResource('photos', 'PhotoController');
```

You may register many API resource controllers at once by passing an array to the `apiResource` method:

```php
Route::apiResources([
    'photos' => 'PhotoController',
    'posts' => 'PostController',
]);
```

To quickly generate an API resource controller that doesn't include the `create` or `edit` methods, use the `--api` swtich when executing the `make:controller` command:

```shell
php artisan make:controller API/PhotoController --api
```

##### Naming Resource Routes

By default, all resource controller actions have a route name; However, you can override these names by passing a `names` array with your options:

```php
Route::resource('photo', 'PhotoController')->names([
    'create' => 'photos.build'
]);
```

##### Naming Resource Route Parameters

By default, `Route::resource` will create the route parameters for your resource routes based on the "singularized" version of the resource name. You can easily override this on a per resource basis by using the `parameters` method. The array passed into the `parameters` method should be an associative array of resource names and parameter names:

```php
Route::resource('users', 'AdminUserController')->parameters([
    'users' => 'admin_user'
]);
```

The example above generates the following URIs for the resource's `show` route:

```php
/users/{admin_user}
```

##### Localizing Resource URIs

By default, `Route::resource` will create resource URIs using English verbs. If you need to localize the `create` and `edit` action  verbs, you may use the `Route::resourceVerbs` method. This may be done in the `boot` method of your `AppServiceProvider`:

```php
use Illuminate\Support\Facades\Route;

/**
 * Bootstrap any application services.
 *
 * @return  void
 */
public function boot()
{
    Route::resourceVerbs([
        'create' => 'crear',
        'edit' => 'editar',
    ]);
}
```

Once the verbs have been customized, a resource route registration such as `Route::resource('fotos', 'PhotoController')` will produce the following URIs:

```php
/fotos/crear
/fotos/{foto}/editar
```



##### Supplementing Resource Controllers

If you need to add additional routes to a resource controller beyond the default set of resource routes, you should define those routes before your call to `Route::resource`; otherwise, the routes defined by the `resource` method may unintentionally take precedence over your supplemental routes:

```php
Route::get('photos/popular', 'PhotoController@method');
Route::resource('photos', 'PhotoController');
```

> Remember to keep your controllers focused. If you find yourself routinely needing methods outside of the typical set of resource actions, consider splitting your controller into two, smaller controllers.

#### Dependency Injection & Controllers

###### Constructor Injection

The Laravel `service container` is used to resolve all Laravel controllers. As a result, you are able to type-hint any dependencies your controller may need in its constructor. The declared dependencies will automatically be resolved and injected into the controller instance:

```php
<?php
    
namespace App\Http\Controllers;

use App\Repositories\UserRepository;

class UserController extends Controller
{
    /**
     * The user repository instance.
     */
    protected $users;
    
    /**
     * Create a new controller instance.
     *
     * @param  UserRepository  $users
     * @return  void
     */
    public function __construct(UserRepository $users)
    {
        $this->users = $users;
    }
}
```

You may also type-hint any `Laravel contract`. If the container can resolve it, you can type-hint it. Depending on your application, injecting your dependencies into your controller may provide better testability.

###### Method Injection

In addition to constructor injection, you may also type-hint dependencies on your controller's methods. A common use-case for method injection is injecting the `Illuminate\Http\Request` iinstance into your controller methods:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    /**
     * Store a new user.
     *
     * @param  Request  $request
     * @return  $response
     */
    public function store(Request $request)
    {
        $name = $request->name;
        //
    }
}
```

If your controller method is also expecting input from a route parameter, list your route arguments after your other dependencies. For example, if your route is defined like so:

```php
Route::get('user/{id}', 'UserController@update');
```

You may still type-hint the `Illuminate\Http\Request` and access your `id` parameter by defining your controller method as follows:

```php
<?php
    
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    /**
     * Update the given user.
     *
     * @param  Request  $request
     * @param  String  $id
     * @return  Response
     */
    public function update(Request $request, $id)
    {
        //
    }
}
```

#### Route Caching

> Closure based routes cannot be cached. To user route caching, you must convert any Closure routes to controller classes.

If your application is exclusively using controller based routes, you should take advantage of Laravel's route cache. Using the route cache will drastically decrease the amount of time it takes to register all of your application's routes. In some cases, your route registration may even be up to 100 times faster. To generate a route cache, just execute the `route:cache` Artisan command:

```shell
php artisan route:cache
```

After running this command, your cached routes file will be loaded on every request. Remember, if you add any new routes you will need generate a fresh route cache. Because of this, you should only run the `route:cache` command during your project's deployment.

And we can use the `route:clear` to clear the route cache:

```shell
php artisan route:clear
```



### 4.5 Requests

#### Accessing The Request

To obtain an instance of the current HTTP request via dependency injection, you should type-hint the `Illuminate\Http\Request` class on your controller method. The incoming request instance will automatically be injected by the `service container`:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    /**
     * Store a new user.
     *
     * @param  Request  $request
     * @return  Response
     */
    public function store(Request $request)
    {
        $name = $request->input('name');
        
        //
    }
}
```

###### Dependency injection & Route Parameters

If your controller method is also expecting input from a route parameter you should list your route parameters after your other dependencies. For example, if your route is defined like so:

```php
Route::put('/user/{id}', 'UserController@update');
```

You may still type-hint the `Illuminate\Http\Request` and access your route parameter `id` by defining your controller method as follows:

```php
<?php
    
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    /**
     * Update the specified user.
     *
     * @param  Request  $request
     * @param  string  $id
     * @return  Response
     */
    public function update(Request $request, $id)
    {
        //
    }
}
```

###### Accessing The Request via Route Closures

You may also type-hint the `Illuminate\Http\Request` class on a route Closure. The service container will automatically inject the incoming request into the Closure when it's executed:

```php
use Illuminate\Http\Request;

Route::get('/', function (Request $request) {
    //
});
```

##### Request Path & Method

The `Illuminate\Http\Request` instance provides a variety of methods for examining the HTTP request for your application and extends the `Symfony\Component\HttpFoundation\Request` class. We will discuss a few of the most important methods below.

###### Retrieving the Request Path

The `path` method returns the request's path information. So, if the incoming request is targeted at `http://domain.com/foo/bar`, the `path` method will return `foo/bar`:

```php
$uri = $request->path();
```

The `is` method allows you to verify that the incoming request path matches a given pattern. You may use the `*` character as a wildcard when utilizing this method:

```php
if($request->is('admin/*')) {
    //
}
```

###### Retrieving the Request URL

To retrieve the full URL for the incoming request you may use the `url` or `fullUrl` methods. The `url` method will return the URL without the query string, while the `fullUrl` method includes the query string:

```php
// Without Query String..
$url = $request->url();
// With Query String...
$url = $request->fullUrl();
```

###### Retrieving The Request Method

The `method` method will return the HTTP verb for the request. You may use the `isMethod` method to verify that the HTTP verb matches a given string:

```php
$method = $request->method();
if($request->isMethod('post')) {
    //
}
```



##### PSR-7 Requests

The `PSR-7 standard` specifies interfaces for HTTP messages, including requests and responses. If you would like to obtain an instance of a PSR-7 request instead of a Laravel request, you will first need to install a few libraries. Laravel uses the Symfony HTTP Message Bridge component to convert typical Laravel requests and responses into PSR-7 compatible implementations;:

```shell
composer require symfony/psr-http-message-bridge
composer require zendframework/zend-diactoros
```

Once you have installed these libraries, you may obtain a PSR-7 request by type-hinting the request interface on your route Closure or controller method:

```php
use Psr\Http\Message\ServerRequestInterface;

Route::get('/', function (ServerRequestInterface $request) {
    //
});
```

> If you return a PSR-7 response instance from a route or controller, it will automatically be converted back to a Laravel response instance and be displayed by the framework.

#### Input Trimming & Normalization

By default, Laravel includes the `TrimStrings` and `ConvertEmtpyStringsToNull` middleware in your application's global middleware stack. These middleware are listed in the stack by the `App\Http\Kernel` class. These middleware will automatically trim all incoming string fields on the request, as well as convert any empty string fields to `null`. This allows you to not have to worry about these normalization concerns in your routes and controllers.

If you would like to disable this behavior, you may remove the two middleware from your application's middleware stack by removing them from the `$middleware` property of your `App\Http\Kernel` class.

#### Retrieving Input

###### Retrieving All Input Data

You may also retrieve all of the input data as an `array` using the `all` method:

```php
$input = $request->all();
```

###### Retrieving An Input Value

Using a  few simple methods, you may access all of the user input from your `Illuminate\Http\Request` instance without worrying about which HTTP verb was used for the request. Regardless of the HTTP verb, the `input` method may be used to retrieve user input:

```php
$name = $request->input('name');
```

You may pass a default  value as the second argument to the `input` method. This value will be returned if the requested input value isn't present on the request.

```php
$name = $request->input('name', 'Sally');
```

When working with forms that contain array inputs, use "dot" notation to access the arrays:

```php
$name = $request->input('products.0.name');
$names = $request->input('products.*.name');
```

You may call the `input` method without any arguments in order to retrieve all of the input values as an associative array:

```php
$input = $request->input();
```

###### Retrieving Input From the Query String

While the `input` method retrieves values from entire request payload(including the query string), the `query` method will only retrieve values from the query string:

```php
$name = $request->query('name');
```

If the requested query string value data isn't present, the second argument to this method will be returned:

```php
$name = $request->query('name', 'Helen');
```

You may call the `query` method without any arguments in order to retrieve all of the query string values as an associative array:

```php
$query = $request->query();
```

###### Retrieving  Input via Dynamic Properties

You may also access user input using dynamic properties on the `Illuminate\Http\Request` instance. For example, if one of your application's forms contains a `name` field, you may access the value of the field like so:

```php
$name = $request->name;
```

When using dynamic properties, Laravel will first look for the parameter's value in the request payload. If it isn't present, Laravel will search for the field in the route parameters.

###### Retrieving JSON Input Values

When sending JSON requests to your application, you may access the JSON data via the `input` method as long as the `Content-Type` header of the request is properly set to `application/json`. You may even use "dot" syntax to dig into JSON arrays:

```php
$name = $request->input('user.name');
```

###### Retrieving a portion of the input data

If you need to retrieve a subset of the input data, you may use the `only` and `except` methods. Both of these methods accept a single `array` or a dynamic list of arguments:

```php
$input = $request->only(['username', 'password']);
$input = $request->only('username', 'password');
$input = $request->except(['credit_card']);
$input = $request->except('credit_card');
```

> The `only` method returns all of the key/value pairs that you request; however, it won't return key/value pairs that aren't present on the request.

###### Determining If An Input Value is Present

You should use the `has` method to determine if a value is present on the request. The `has` method returns `true` if the value is present on the request:

```php
if($request->has('name')) {
    //
}
```

When given an array, the `has` method will determine if all of the specified values are present:

```php
if($request->has(['name', 'email'])) {
    //
}
```

If you would like to determine if a value is present on the request and isn't empty, you may use the `filled` method:

```php
if($request->filled('name')) {
    //
}
```

##### Old Input

Laravel allows you to keep input from one request during the next request. This feature is particularly useful for re-populating forms after detecting validation errors. However, if you are using Laravel's included `validation features`, it is unlikely you will need to manually use these methods, as some of Laravel's built-in validation facilities will call them automatically.

###### Flashing Input To The Session

The `flash` method on the `Illuminate\Http\Request` class will flash the current input to the `session` so that it is available during the user's next request to the application:

```php
$request->flash();
```

You may also use the `flashOnly` and `flashExcept` methods to flash a subset of the request data to the session. These methods are useful for keeping sensitive information such as passwords out of the session:

```php
$request->flashOnly(['username', 'email']);
$request->flashExcept('password');
```

###### Flashing Input Then Redirecting

Since you often will want to flash input to the session and then redirect to the previous page, you may easily chain input flashing onto a redirect using the `withInput` method:

```php
return redirect('form')->withInput();
return redirect('form')->withInput(
	$request->except('password');
);
```

###### Retrieving Old Input

To retrieve flashed input from the previous request, use the `old` method on the `Request` instance. The `old` method will pull the previously flashed input data from the `session`:

```php
$username = $request->old('username');
```

Laravel also provides a global  `old` helper. If you are displaying old input within a `Blade template`, it's more convenient to use the `old` helper. If no old input exists for the given field, `null` will be returned:

```html
<input type="text" name="username" value="{{ old('username') }}">
```

##### Cookies

###### Retrieving Cookies From Requests

All cookies created by the Laravel framework are encrypted and signed with an authentication code, meaning they will be considered invalid if they have been changed by the client. To retrieve a cookie value from the request, use the `cookie` method on a `Illuminate\Http\Request` instance:

```php
$value = $request->cookie('name');
```

Alternatively, you may use the `Cookie` facade to access cookie values:

```php
$value = Cookie::get('name');
```

###### Attaching Cookies To Responses

You may attach a cookie to an outgoing `Illuminate\Http\Response` instance using the `cookie` method. You should pass the name, value, and number of minutes the cookie should be considered valid to this method:

```php
return response('Hello world')->cookie(
	'name', 'value', $minutes
);
```

The `cookie` method also accepts a few more arguments which are used less frequently. Generally, these arguments have the same purpose and meaning as the arguments that would be given to PHP's native `setcookie` method:

```php
return response('Hello world')->cookie(
	'name', 'value', $minutes, $path, $domain, $secure, $httpOnly
);
```

Alternatively, you can use the `Cookie` facade to "queue" cookies for attachment to the outgoing response from your application. The `queue` method accepts a `Cookie` instance or the arguments needed to create a `Cookie` instance. These cookies will be attached to the outgoing response before it is sent to the browser:

```php
Cookie::queue(Cookie::make('name', 'value', $minutes));
Cookie::queue('name', 'value', $minutes);
```

###### Generating Cookie Instances

If you would like to generate a `Symfony\Component\HttpFoundation\Cookie` instance that can be given to a response instance at a later time, you may use the global `cookie` helper. This cookie will not be sent back to the client unless it's attached to a response instance:

```php
$cookie = cookie('name', 'value', $minutes);
return response('Hello world')->cookie($cookie);
```



#### Files

##### Retrieving Uploaded Files

You may access uploaded files from a `Illuminate\Http\Request` instance using the `file` method or using dynamic properties. The `file` method returns an instance of the `Illuminate\Http\UploadedFile` class, which extends the PHP `SqlFileInfo` class and provides a variety of methods for interacting with the file:

```php
$file = $request->file('photo');
$file = $request->photo;
```

You may determine if a file is present on the request using the `hasFile` method:

```php
if($request->hasFile('photo')) [
    //
]
```

###### Validating Successful Uploads

In addition to checking if the file is present, you may verify that there were no problems uploading the file via the `isValid` method:

```php
if($request->file('photo')->isValid()) {
    //
}
```

###### File Paths & Extensions

The `UploadedFile` class also contains methods for accessing the file's fully-qualified path and its extension. The `extension` method will attempt to guess the file's extension based on its contents. This extension may be different from the extension that was supplied by the client:

```php
$path = $request->photo->path();
$extension = $request->photo->extension();
```

###### Other File Methods

There are a variety of other methods available on `UploadedFile` instances. Check out the  [API document ](https://api.symfony.com/3.0/Symfony/Component/HttpFoundation/File/UploadedFile.html) for more information regarding these methods.

##### Storing Uploaded Files

To store an uploaded file, you will typically use one of your configured `filesystem(Digging Deeper/Filesystem)`. The `UploadedFile` class has a `store` method which will move an uploaded file to one of your disks, which may be a location on your local filesystem or even a cloud storage location like Amazon S3.

The `store` method accepts the path where the file should be stored relative to the filesystem's configured root directory. This path shouldn't contain a file name, since a unique ID will automatically be generated to serve as the file name.

The `store` method also accepts an optional second argument for the name of the disk that should be used to store the file. The method will return the path of the file relative to the disk's root:

```php
$path = $request->photo->store('images');
$path = $request->photo->store('images', 's3');
```

If you don't want a file name to be automatically generated, you may use the `storeAs` method, which accepts the path, file name, and disk name as its arguments:

```php
$path = $request->photo->storeAs('images', 'filename.ext');
$path = $request->photo->storeAs('images', 'filename.ext', 's3');
```

#### Configuring Trusted Proxies

When running your applications behind a load balancer that terminates TLS/SSL certificates, you may notice your application sometimes doesn't generate HTTPS links. Typically this is because your application is being forwarded traffic from your load balancer on port 80 and doesn't know it should generate secure links.

 To solve this, you may use the `App\Http\Middleware\TrustProxies` middleware that is included in your Laravel application, which allows you to quickly customize the load balancers or proxies that should be trusted by your application. Your trusted proxies should be listed as an array on the `$proxies` property of this middleware. In addition to configuring the trusted proxies, you may congfigure the proxy `$headers` that should be trusted:

```php
<?php
    
namespace App\Http\Middleware;

use Illuminate\Http\Request;
use Fideloper\Proxy\TrustProxies as Middleware;

class TrustProxies extends Middleware
{
    /**
     * The trusted proxies for this application
     *
     * @var  array
     */
    protected $proxies = [
        '192.168.1.1',
        '192.168.1.2',
    ];
    
    /**
     * The headers that should be used to detect proxies.
     *
     * @var  string
     */
    protected $headers = Request::HEADER_X_FORWARDED_ALL;
}
```

> If you are using AWS Elastic Load Balancing, your `$headers` value should be `Request::HEADER_X_FORWARDED_AWS_ELB`. For more information on the constants that may be used in the `$headers` property, check out Symfony's documentation on `trusting proxies`.

###### Trusting All Proxies

If you are using Amazon AWS or other cloud load balancer provider, you may not know the IP addresses of your actual balancers. In this case, you may use `*` to trust all proxies:

```php
/**
 * The trusted proxies for this application.
 *
 * @var  array
 */
protected $proxies = '*';
```



### 4.6 HTTP Responses

#### Creating Responses

###### Strings & Arrays

All routes and controllers should return a response to be sent back to the user's browser. Laravel provides several different ways to return responses. The most basic response is returning a string from a route or controller. The framework will automatically convert the string into a full HTTP response:

```php
Route::get('/', function () {
    return 'Hello world';
});
```

In addition to returning strings from your routes and controllers, you may also return arrays. The framework will automatically convert the array into a JSON response:

```php
Route::get('/', function () {
    return [1, 2, 3];
});
```

> Did you know you can also return `Eloquent collections` from your routes or controllers? They will automatically be converted to JSON. Give it a shot!

###### Response Objects

Typically, you won't just be returning simple strings or arrays from your route actions. Instead, you will be returning full `Illuminate\Http\Response` instances or `views`.

Returning a full `Response` instance allows you to customize the response's HTTP status code and headers. A `Response` instance inherits from the `Symfony\Component\HttpFoundation\Response` class, which provides a variety of methods for building HTTP responses.

```php
Route::get('home', function () {
    return response('Hello world', 200)
        ->header('Content-Type', 'text/plain');
});
```



##### Attaching Headers To Responses

Keeping in mind that most response methods are chainable, allowing for the fluent construction of response instances. For example, you may use the `header` method to add a series of headers to the response before sending it back to the user:

```php
return response($content)
    ->header('Content-Type', $type)
    ->header('X-Header-One', 'Header Value')
    ->header('X-Header-Two', 'Header Value');
```

Or, you may use the `withHeaders` method to specify an array of headers to be added to the response:

```php
return response($content)
    ->withHeaders([
        'Content-Type' => $type,
        'X-Header-One' => 'Header Value',
        'X-Header-Two' => 'Header Value',
    ]);
```



##### Attaching Cookies To Responses

The `cookie` method on response instances allows you to easily attach cookies to the response. For example, you may use the `cookie` method to generate a cookie and fluently attach it to the response instance like so:

```php
return response($content)
    ->header('Content-Type', $type)
    ->header('name', 'value', $minutes);
```

The `cookie` method also accepts a few more arguments which are used less frequently. Generally, these arguments have the same purpose and meaning as the arguments that would be given to PHP's native `setcookie` method:

```php
->cookie($name, $value, $minutes, $path, $domain, $secure, $httpOnly)
```

Alternatively, you can use the `Cookie` facade to "queue" cookies for attachment to the outgoing response from your application. The `queue` method accepts a `Cookie` instance or the arguments needed to create a `Cookie` instance. These cookies will be attached to the outgoing response before it's sent to the browser:

```php
Cookie::queue(Cookie::make('name', 'value', $minutes));
Cookie::queue('name', 'value', $minutes);
```



##### Cookies & Encryption

By default, all cookies generated by Laravel are encrypted and signed so that they can't be modified or read by the client. If you would like to disable encryption for a subset of cookies generated by your application, you may use the `@except` property of the `App\Http\Middleware\EncryptCookies` middleware, which is located in the `app/Http/Middleware` directory:

```php
/**
 * The names of the cookies that shouldn't be encrypted.
 *
 * @var  array
 */
protected $except = [
    'cookie_name',
];
```



#### Redirects

Redirect responses are instances of the `Illuminate\Http\RedirectResponse` class, and contain the proper headers needed to redirect the user to another URL. There are several ways to generate a `RedirectResponse` instance. The simplest method is to use the global `redirect` helper:

```php
Route::get('dashboard', function () {
    return redirect('home/dashboard');
});
```

Sometimes you may wish to redirect the user to their previous location, such as when a submitted form is invalid. You may do so by using the global `back` helper function. Since this feature utilizes the `session`, make sure the route calling the `back` function is using the `web` middleware group or has all of the session middleware applied:

```php
Route::post('user/profile', function () {
    // Validate the request...
    
    return back()->withInput();
});
```

##### Redirecting To Named Routes

When you call the `redirect` helper with no parameters, an instance of `Illuminate\Routing\Redirector` is returned, allowing you to call any method on the `Redirector` instance. For example, to generate a `RedirectResponse` to a named route, you may use the `route` method:

```php
return redirect()->route('login');
```

If your route has parameters, you may pass them as the second argument to the `route` method:

```php
// For a route with the following URI: profile/{id}
return redirect()->route('profile', ['id' => 1]);
```

###### Populating Parameters Via Eloquent Models

If you are redirecting to a route with an "ID" parameter that is being populated from an Eloquent model, you may pass the model itself. The ID will be extracted automatically:

```php
// For a route with the following URI: profile/{id}
return redirect()->route('profile', [$user]);
```

If you would like to customize the value that's placed in the route parameter, you should override the `getRouteKey` method on your Eloquent model:

```php
/**
 * Get the value of the model's route key.
 *
 * @return mixed
 */
public function getRouteKey()
{
    return $this->slug;
}
```

##### Redirecting To Controller Actions

You may also generate redirects to `controller actions`. To do so, pass the controller and action name to the `action` method. Remember, you don't need to specify the full namespace to the controller since Laravel's `RouteServiceProvider` will automatically set the base controller namespace:

```php
return redirect()->action('HomeController@index');
```

If your controller route requires parameters, you may pass them as the second argument to the `action` method:

```php
return redirect()->action(
	'UserController@profile', ['id' => 1]
);
```

##### Redirecting To External Domains

Sometimes you may need to redirect to a domain outside of your application. You may do so by calling the `away` method, which creates a `RedirectResponse` without any additional URL encoding, validation, or verification:

```php
return redirect()->away('https://www.google.com');
```

##### Redirecting With Flashed Session Data

Redirect to a new URL and `flashing data to the session` are usually done at the same time. Typically, this is done after successfully performing an action when you flash a success message to the session. For convenience, you may create a `RedirectResponse` instance and flash data to the session in a single, fluent method chain:

```php
Route::post('user/profile', function () {
    // Update the user's profile...
    return redirect('dashboard')->with('status', 'Profile updated!');
});
```

After the user is redireted, you may display the flashed message from the `session`. For example, using `Blade Syntax`.

```html
@if(session('status))
	<div class="alert alert-success">
        {{ session('status') }}
	</div>
@endif
```



#### Other Response Types

The `response` helper may be used to generate other types of response instances. When the `response` helper is called without arguments, an implementation of the `Illuminate\Contracts\Routing\ResponseFactory` **contract** is returned. This contract provides several helpful methods for generating responses.

##### View Responses

If you need control over the response's status and headers but also need to return a `view` as the response's content, you should use the `view` method:

```php
return response()
    ->view('hello', $data, 200)
    ->header('Content-Type', $type);
```

Of course, if you don't need to pass a custom HTTP status code or custom headers, you should use the global `view` helper function.

##### JSON Responses

The `json` method will automatically set the `Content-Type` header to `application/json`, as well as convert the given array to JSON using the `json_encode` PHP function:

```php
return response()->json([
    'name' => 'Abigail',
    'state' => 'CA'
]);
```

If you would like to create a JSONP response, you may use the `json` method in combination with the `withCallback` method:

```php
return response()
    ->json(['name' => 'Abigail', 'state' => 'CA'])
    ->withCallback($request->input('callback'));
```

##### File Downloads

The `download` method may be used to generate a response that forces the user's browser to download the file at the given path. The `download` method accepts a file name as the second argument to the method, which will determine the file name that is seen by the user downloading the file. Finally, you may pass an array of HTTP headers as the third argument to the method:

```php
return response()->download($pathToFile);
return response()->download($pathToFile, $name, $headers);
return response()->download($pathToFile)->deleteFileAfterSend();
```

> Symfony HTTP Foundation, which manages file downloads, requires the file being downloaded to have an ASCII file name.

###### Streamed Downloads

Sometimes you may wish to turn the string response of a given operation into a downloadable response without having to write the contents of the operation to disk. You may use the `streamDownload` method in this scenario. This method accepts a callback, file name, and an optional array of headers as its arguments:

```
return response()->streamDownload(function () {
    echo Github::api('repo')
    ->contents()
    ->readme('laravel', 'laravel')['contents'];
}, 'laravel-readme.md');
```

##### File Responses

The `file` method may be used to display a file, such as an image or PDF, directly in the user's browser instead of initiating a download. This method accepts the path to the file as its first argument and an array of headers as its second argument:

```php
return response()->file($pathToFile);
return response()->file($pathToFile, $headers);
```

#### Response Macros

If you would like to define a custom response that you can re-use in a variety of your routes and controllers, you may use the `macro` method on the `Response` facade. For example, from a `service provider's` **boot** method:

```php
<?php
    
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\Response;

class ResponseMacroServiceProvider extends ServiceProvider
{
    /**
     * Register the application's response macros.
     *
     * @return  void
     */
    public function boot()
    {
        Response::macro('caps', function ($value) {
            return Response::make(strtoupper($value));
        });
    }
}
```

The `macro` function accepts a name as its first argument, and a Closure as its second. The macro's Closure will be executed when calling the macro name from a `ResponseFactory` implementation or the `response` helper:

```php
return response()->caps('foo');
```



### 4.7 Views

#### Creating Views

> Looking for more information on how to write Blade templates? Check out the full `Blade documentation` to get started.

Views contain the HTML served by your application and separate your controller / application logic from your presentation logic. Views are stored in the `resources.views` directory. A simple view might look something like this:

```html
<!-- View stored in resources/views/greeting.blade.php -->
<html>
    <body>
        <h1>
            Hello, {{ $name }}
        </h1>
    </body>
</html>
```

Since this view is stored at `resources/views/greeting.blade.php`, we may return it using the global `view` helper like so:

```php
Route::get('/', function () {
    return view('greeting', ['name' => 'James']);
});
```

As we can see, the first argument passed to the `view` helper corresponds to the name fo the view file in the `resources/views` directory. The second argument is an array of data that should be made available to the view. In this case, we are passing the `name` variable, which is displayed in the view using `Blade Syntax`.

Views may also be nested within sub-directories of the `resources/views` directory. "Dot" notation may be used to reference nested views. For example, if your view is stored at `resources/views/admin/profile.blade.php`, you may reference it like so:

```php
return view('admin.profile', $data);
```

######  Determine if A View Exists

If you need to determine if a view exists, you may use the `View` facade. The `exists` method will return `true` if the view exists.

```php
use Illuminate\Support\Facades\View;

if(View::exists('emails.customer')) {
    //
}
```

###### Creating The first Available View

Using the `first` method, you may create the first view that exists in a given array of views. This is useful if your application or package allows views to be customized or overwritten:

```php
return view()->first(['custom.admin', 'admin'], $data);
```

You may also call this method via the `View` **facade**:

```php
use Illuminate\Support\Facades\View;

return View::first(['custom.admin', 'admin'], $data);
```



#### Passing Data To Views

As we saw in the previous examples, you may pass an array of data to views:

```php
return view('greetings', ['name' => 'Bob']);
```

When passing information in this manner, the data should be an array with key/value pairs. Inside your view, you can then access each value using its corresponding key, such as <?php echo $keys; ?>. As an alternative to passing a complete array of data to the `view` helper function, you may use the `with` method to add individual pieces of data to the view:

```php
return view('greeting')->with('name', 'Bob');
```



##### Sharing Data With All Views

Occasionally, you may need to share a piece of data with all views that are rendered by your application. You may do so using the view facade's `share` method. Typically, you should place calls to `share` within a service provider's `boot` method. You are free to add them to the `AppServiceProvider` or generate a separate service provider to house them:

```php
<?php
    
namespace App\Providers;

use Illuminate\Support\Facades\View;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return  void
     */
    public function boot()
    {
        View::share('key', 'value');
    }
    
    /**
     * Register the service provider.
     *
     * @return  void
     */
    public function register()
    {
        //
    }
}
```



#### View Composers

View composers are callbacks or class methods that are called when a view is rendered. If you have data that you want to be bound to a view each time that view is rendered, a view composer can help you organize that logic into a single location.

For this example, let's register the view composers within a `service provider`. We'll use the `View` facade to access the underlying `Illuminate\Contracts\View\Factory` contract implementation. Remember, Laravel doesn't include a default directory for view composers. We are free to organize them however you wish. For example, you could create an `app/Http/View/Composers` directory:

```php
<?php
    
namespace App\Providers;

use Illuminate\Support\Facades\View;
use Illuminate\Support\ServiceProvider;

class ViewServiceProvider extends ServiceProvider
{
    /**
     * Register bindings in the container.
     *
     * @return  void
     */
    public function boot()
    {
        // Using class based composers...
        View::composer(
        	'profile', 'App\Http\View\Composers\ProfileComposer'
        );
        
        // Using Closure based composers...
        View::composer('dashboard', function ($view) {
            //
        });
    }
    
    /**
     * Register the service provider.
     *
     * @return  void
     */
    public function register()
    {
        //
    }
}
```

> Remember, if you create a new service provider to contain your view composer registrations, you will need to add the service provider to the `providers` array in the `config/app.php` configuration file.

Now that we have registered the composer, the `ProfileComposer@compose` method will be executed each time the `profile` view is being rendered. So, let's define the composer class:

```php
<?php
    
namespace App\Http\View\Composers;

use Illuminate\View\View;
use App\Repositories\UserRepository;

class ProfileComposer
{
    /**
     * The user repository implementation.
     *
     * @var  UserRepository
     */
    protected $users;
    
    /**
     * Create a new profile composer.
     *
     * @param  UserRepository  $users
     * @return  void
     */
    public function __construct(UserRepository $users)
    {
        // Dependencies automatically resolved by service container...
        $this->users = $users;
    }
    
    /**
     * Bind data to the view.
     *
     * @param  View  $view
     * @return  void
     */
    public function compose(View $view)
    {
        $view->with('count', $this->users->count());
    }
}
```

Just before the view is rendered, the composer's `compose` method is called with the `Illuminate\View\View` instance. You may use the `with` method to bind data to the view.

> All view composers are resolved via the `service container`, so you may type-hint any dependencies you need within a composer's constructor.

###### Attaching A composer To multiple views

You may attach a view composer to multiple views at once by passing an array of views as the first argument to the `composer` method:

```php
View::method(
	['profile', 'dashboard'],
    'App\Http\View\Composers\MyViewComposer'
);
```

The `composer` method also accepts the `*` character as a wildcard, allowing you to attach a composer to all views:

```php
View::composer('*', function ($view) {
    //
});
```

###### View Creators

View **creators** are very similar to view composers; however, they are executed immediately after the view is instantiated instead of waiting until the view is about to render. To register a view creator, use the `creator` method:

```php
View::creator('profile', 'App\Http\View\Creators\ProfileCreator');
```



### 4.8 URL Generation

### 4.9 Session

### 4.10 Validation

### 4.11 Error Handling

### 4.12 Logging

## 5.Frontend

### 5.1 Blade Templates

#### Introduction

Blade is the simple, yet powerful templating engine provided with Laravel. Unlike other popular PHP templating engines, Blade doesn't restrict you from using plain PHP code in you views. In fact all Blade views are compiled into plain PHP code and cached until they are modified, meaning Blade adds essentially zero overhead to your application. Blade view files use the `.blade.php` file extension and are typically stored in the `resources/views` directory.

#### Template Inheritance

##### Defining A Layout

Two of the primary benefits of using Blade are template inheritance and sections. To get started ,let's take a look at a simple example. First, we will examine a "master" page layout. Since most web applications maintain the same general layout across various pages, it's convenient to define this layout as a single Blade view:

```php+HTML
<!-- Stored in resources/views/layouts/app.blade.php -->
<html>
    <head>
        <title>appName - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
        	This is the master siderbar.
        @show
        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```

As we can see, this file contains typical HTML markup. However, take not of the `@section` and `@yield` directives. The `@section` directive, as the name implies, defines a section of content, while the `@yield` directive is used to display the contents of a given section.

Now that we have defined a layout for our application, let's define a child page that inherits the layout.

##### Extending A Layout

When defining a child view, use the Blade `@extends` directive to specify which layout the child view should "inherit". Views which extend a Blade layout may inject content into the layout's sections using `@section` directives. Remember. as seen in the example above, the contents of these sections will be displayed in the layout using `@yield`:

```php+HTML
<!-- Stored in resources/views/child.blade.php -->
@extends('layouts.app)
@section('title', 'Page title')
@section('sidebar')
	@parent
	<p>
        This is appended to the master siderbar.
	</p>
@endsection
@section('content')
<p>This is my body content.</p>
@endsection
```

In this example, the `sidebar` section is utilizing the `@parent` directive to append(rather than overwriting) content to the layout's siderbar. The `@parent` directive will be replaced by the content of the layout when the view is rendered.

> Contrary to the previous example, this `sidebar` section ends with `@endsection` instead of `@show`. The `@endsection` directive will only define a section while `@show` will define and **immediately yield** the section. 

Blade views may be returned from routes using the global `view` helper:

```php
Route::get('blade', function () {
   	return view('child'); 
});
```



#### Components & Slots

Components and slots provide similar benefits to sections and layouts; however, some may find the mental model of components and slots easier to understand. First, let's imagine a reusable "alert" component we would like to reuse throughout out application:

```php+HTML
<!-- /resources/views/alert.blade.php -->
<div class="alert alert-danger">
    {{ $slot }}
</div>
```

The `{{ $slot }}` variable will contain the content we wish to inject into the component. Now, to construct this component, we can use the `@component` Blade directive:

```php+HTML
@component('alert')
	<strong>Whoops!</strong> Something went wrong!
@endcomponent
```

Sometimes it's helpful to define multiple slots for a component. Let's modify our alert component to allow for the injection of a "title". Named slots may be displayed by "echoing" the variable that matches their name:

```php+HTML
<!-- /resources/views/alert.blade.php -->
<div class="alert alert-danger">
    <div class="alert-title">
        {{ $title }}
    </div>
    {{ $slot }}
</div>
```

Now, we can inject content into the named slot using the `@slot` directive. Any content not within a `@slot` directive will be passed to the component in the `@slot` variable:

```php+HTML
@component('title')
	@slot('title)
		Forbidden
	@endslot
	You aren't allowed to access their resource!
@endcomponent
```

##### Passing Additional Data to components

Sometimes you may need to pass additional data to a component. For this reason, you can pass an array of data as the second argument to the `@component` directive. All of the data will be made available to the component template as variables:

```php+HTML
@component('alert', ['foo' => 'bar'])
	...
@endcomponent
```

##### Aliasing Components

If your Blade components are stored in a sub-directory, you may wish  to alias them for easier access. For example, imagine a Blade component that is stored at `resources/views/component/alert.blade.php`. You may use the `component` method to alias the component from `components.alert` to `alert`. Typically, this should be done in the `boot` method of your `AppServiceProvider`:

```php
use Illuminate\Support\Facades\Blade;

Blade::component('components.alert', 'alert');
```

Once the component has been aliased, you may render it using a directive.

```php+HTML
@alert(['type' => 'danger'])
	You are not allowed to access this resource!
@endalert
```

You may omit the component parameters if it has no additional slots:

```php+HTML
@alert
	You are not allowed to access this resource!
@endalert
```



#### Displaying Data

You may display data passed to your Blade views by wrapping the variable in curly braces. For example, given the following route:

```php
Route::get('greeting', function () {
   	return view('welcome', ['name' => 'Samantha']); 
});
```

You may display the contents of the `name` variable like so:

```php+HTML
Hello,{{ $name }}.
```

> Blade `{{ }}` statements are automatically sent through PHP's `htmlspecialchars` function to prevent XSS attacks.

You aren't limited to displaying the contents of the variables passed to the view. You may also echo the results of any PHP function. In fact, you can put any PHP code you wish inside of a Blade echo statement:

```php+HTML
The current UNIX timestamp is {{ time() }}.
```

##### Dispalying unescaped Data

By default, Blade `{{ }}` statements are automatically sent through PHP's `htmlspecialchars` function to prevent XSS attacks. If you don't want your data to be escaped, you may use the following syntax:

```php+HTML
Hello, {!! $name !!}.
```

> Be very careful when echoing content that's supplied by users of your application. Always use the escaped, double curly brace syntax to prevent XSS attacks when displaying user supplied data.

##### Rendering JSON

Sometimes you may pass an array to your view with the intension of rendering it as JSON in order to initialize a JavaScript variable. For example:

```javascript
<script>
	var app = <?php echo json_encode($array); ?>;
</script>
```

However, instead of manually calling `json_encode`, you may use the `@json` Blade directive:

```javascript
<script>
	var app = @json($array)    
</script>
```

##### HTML Entity Encoding

By default, Blade (and the Laravel `e` helper) will double encode HTML entities. If you would like to disable double encoding, call the `Blade::withoutDoubleEncoding` method from the `boot` method of your `AppServiceProvider`:

```php
<?php
    
namespace App\Providers;

use Illuminate\Support\Facades\Blade;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Blade::withoutDoubleEncoding();
    }
}
```



#### Blade & Javascript Frameworks

Since many JavaScript frameworks also use "curly" braces to indicate a given expression should be displayed in the browser, you may use the `@` symbol to inform the Blade rendering engine an expression should remain untouched. For example:

```php+HTML
<h1>
    Laravel
</h1>
Hello, @{{ name }}.
```

In this example, the `@` symbol will be removed by Blade; However, {{ name }} expression will remain untouched by the Blade engine, allowing it to instead be rendered by your JavaScript framework.

##### The `@verbatim` Directive

If you are displaying JavaScript variables in a large partition of your template, you may wrap the HTML in the `@verbatim` directive so that you don't have to prefix each Blade echo statement with an `@` symbol:

```php+HTML
@verbatim
	<div class="container">
        Hello, {{ name }}
	</div>
@endverbatim
```



#### Control Structures

In addition to template inheritance and displaying data, Blade also provides convenient shortcuts for common PHP control structures, such as conditional statements and loops. These shortcust provide a very clean, terse way of working with PHP control structures, while also remaining familiar to their PHP counterparts.

##### If Statements

You may construct `if` statements using the `@if`, `@elseif`, `@else`, and `@endif` directives. These directives function identically to their PHP counterparts:

```php+HTML
@if(count($records) === 1)
 	I have one record!
@elseif(count($records) > 1)
	I have multiple records!
@else
	I don't have any records!
@endif
```

For convenience, Blade also provides an `@unless` directive:

```php+HTML
@unless(Auth::check())
	You are not signed in.
@endunless
```

In addition to the conditional directives already discussed, the `@isset` and `@empty` directives may be used a s convenient shortcuts for their respective PHP functions:

```php+HTML
@isset($records)
	// $records is defined and is not null...
@endisset
@empty($records)
	// $records is "empty"...
@endempty
```

###### Authentication Directives

The `@auth` and `@guest` directives may be used to quickly determine if the current user is authenticated or is a guest:

```php+HTML
@auth
	// The user is authenticated...
@endauth
@guest
	// The user is not authenticated...
@endguest
```

If needed, you may specify the `authentication guard` that should be checked when using the `@auth` and `@guest` directives:

```php+HTML
@auth('admin')
	// The user is authenticated...
@endauth
@guest('admin')
	// The user is not authenticated...
@endguest
```

###### Section Directives

You may check if a section has content using the `@hasSection` direction:

```php+HTML
@hasSection('navigation')
	<div class="pull-right">
        @yield('navigation')
	</div>
	<div class="clearfix">
	</div>
@endif
```

##### Switch Statements

Switch statements can be constructed using the `@switch`, `@case`, `@break`, `@default` and `@endswitch` directives:

```php+HTML
@switch($i)
	@case(1)
		First case...
		@break
	@case(2)
		Second case...
		@break
	@case(3)
		Third case...
		@break
	@default
		Default case...
@endswitch
```

##### Loops

In addition to conditional statements, Blade provides simple directives for working with PHP's loop structures. Again, each of these directives functions identically to their PHP counterparts:

```php+HTML
@for($i = 0; $i < 10; $i++)
 	The current value is {{ $i }}
@endfor
@foreach($users as $user)
   	<p>This is user {{ $user->id }}</p>
@endforeach
@forelse($users as $User)
	<li>{{ $user->name }}</li>
@empty
	<p>No users</p>
@endforelse
@while(true)
	<p>I am looping forever.</p>
@endwhile
```

> When looping, you may use the `loop variable` to gain valuable information about the loop, such as whether you are in the first or last iteration through the loop.

When using loops you may also end the loop or skip the current iteration:

```php+HTML
@foreach($users as $user)
	@if($user->type == 1)
		@continue
	@endif
	<li>{{ $user->name }}</li>
	@if($user->number == 5)
		@break
	@endif
@endforeach
```

You may also include the condition with the directive declaration in one line:

```php+HTML
@foreach($users as $user)
	@continue($user->type == 1)
	<li>{{ $user->name }}</li>
	@break($user->number == 5)
@endforeach
```

##### The Loop Variable

When looping, a `$loop` variable will be available inside of your loop. This variable provides access to some useful bits of information such as the current loop index and whether this is the first or last iteration through the loop:

```php+HTML
@foreach($users as $user)
	@if($loop->first)
		This is the first iteration.
	@endif
	@if($loop->last)
		This is the last iteration.
	@endif
	<p>This is user {{ $user->id }}</p>
@endforeach
```

If you are in a nested loop, you may access the parent loop's `$loop` variable via the `parent` property:

```php+HTML
@foreach($users as $user)
	@foreach($user->posts as $post)
		@if($loop->parent->first)
			This is first iteration of the parent loop.
		@endif
	@endforeach
@endforeach
```

The `$loop` variable also contains a variety of other useful properties:

| Property         | Description                                            |
| ---------------- | ------------------------------------------------------ |
| $loop->index     | The index of the current loop iteration (starts at 0). |
| $loop->iteration | The current loop iteration (starts at 1).              |
| $loop->remaining | The iterations remaining in the loop.                  |
| $loop->count     | The total number of items in the array being iterated. |
| $loop->first     | Whether this is the first iteration through the loop.  |
| $loop->last      | Whether this is the last iteration through the loop.   |
| $loop->depth     | The nesting level of the current loop.                 |
| $loop->parent    | When in a nested loop, the parent's loop variable.     |

##### Comments

Blade also allows you to define comments in your vies. However, unlike HTML comments, Blade comments are not included in the HTML returned by your application:

```php+HTML
{{-- This comment will not be present in the rendered HTML --}}
```

##### PHP

In some situations, it's useful to embed PHP code into your views. You can use the Blade `@php` directive to execute a block of plain PHP within your template:

```php+HTML
@php
	//
@endphp
```

> Whether Blade provides this feature, using it frequently may be a signal that you have too much logic embedded within your template.

#### Forms

##### CSRF Field

Anytime we define a HTML form in your application, you should include a hidden CSRF token field in the form so that `the CSRF protection` middleware can validate the request. You may use the `@csrf` Blade directive to generate the token field:

```php+HTML
<form method="POST" action="/profile">
   	@csrf 
   	...
</form>
```

##### Method Field

Since HTML forms can't make `PUT`, `PATCH` or `DELETE` requests, you will need to add a hidden `_method` field to spoof these HTTP verbs. The `@method` Blade directive can create this field for you:

```php+HTML
<form action="/foo/bar" method="POST">
    @method('PUT')
    ...
</form>
<form action="/foo/bar2" method="POST">
    @method('PATCH')
    ...
</form>
<form action="/foo/bar3" method="POST">
    @method('DELETE')
    ...
</form>
```

#### Including Sub-Views

Blade's `@include` directive allows you to include a Blade view from within another view. All variables that are available to the parent view will be made available to the included view:

```php+HTML
<div>
    @include('shared.errors)
    <form>
        <!-- Form contents -->
    </form>
</div>
```

Even though the included view will inherit all data available in the parent view, you may also pass an array of extra data to the included view:

```php+HTML
@include('view.name', ['some' => 'data'])
```

If you attempt to `@include` a view which doesn't exist, Laravel will throw an error. If you would like to include a view that may or may not be present, you should use the `@includeIf` directive:

```php+HTML
@includeIf('view.name', ['some' => 'data'])
```

If you would like to `@include` a view depending on a given boolean condition, you may use the `@includeWhen` directive:

```php+HTML
@includeWhen($boolean, 'view.name', ['some' => 'data'])
```

To include the first view that exists from a given array of views, you may use the `includeFirst` directive:

```php+HTML
@includeFirst(['custom.admin', 'admin'], ['some' => 'data'])
```

> You should avoid using the `__DIR__` and `__FILE__` constants in your Blade views, since they will refer to the location of the cached, compiled view.

###### Aliasing includes

If your Blade includes are stored in a sub-directory, you may wish to alias them for easier access. For example, imagine a Blade include that is stored at `resources/views/includes/input.blade.php` with the following content:

```php+HTML
<input type="{{ $type ?? 'text' }}"/>
```

You may use the `include` method to alias the include form `includes.input` to `input`. Typically, this should be done in the `boot` method of your `AppServiceProvider`:

```php
use Illuminate\Support\Facades\Blade;

Blade::include('includes.input', 'input');
```

Once the include has been aliased, you may render it using the alias name as the Blade directive:

```php
@input(['type' => 'email'])
```

##### Rendering Views For Collections

You may combine loops and includes into one line with Blade's `@each` directive:

```php
@each('view.name', $jobs, 'job')
```

The first argument is the view partial to render for each element in the array or collection. The second argument is the array or collection you wish to iterate over, while the third argument is the variable name that will be assigned to the current iteration within the view. So, for example, if you are iterating over an array of `jobs`, typically you will want to access each job as the `key` variable within your view partial.

You may also pass a fourth argument to the `@each` directive. This argument determines the view that will be rendered if the given array is empty.

```php
@each('view.name', $jobs, 'job', 'view.empty')
```

> Views rendered via `@each` don't inherit the variables from the parent view. If the child view requires these variables, you should use `@foreach` and `@include` instead.

#### Stacks

Blade allows you to push to named stacks which can be rendered somewhere else in another view or layout. This can be particularly useful for specifying any JavaScript libraries required by your child views:

```php+HTML
@push('scripts')
	<script src="/example.js"></script>
@endpush
```

you may push to a stack as many times as needed. To render the complete stack contents, pass the name of the stack to the `@stack` directive:

```php+HTML
<head>
    <!-- Head Contents -->
    @stack('scripts')
</head>
```

If you would like to prepend content onto the beginning of a stack, you should use the `@prepend` directive:

```php+HTML
@push('scripts')
	This will be second...
@endpush
// Later...
@prepend('scripts')
	This will be first...往前插入
@endprepend
```



#### Service Injection

The `@inject` directive may be used toe retrieve a service from the Laravel `service container`. The first argument passed to `@inject` is the name of the variable the service will be placed into, while the second argument is the class or interface name of the service you wish toe resolve:

```php+HTML
@inject('metrics', 'App\Services\MetricsService')
<div>
    Monthly Revenue: {{ $metrics->monthlyRevenue() }}.
</div>
```

#### Extending Blade

Blade allows you to define your own custom directives using the `directive` method. When the Blade compiler encounters the custom directive, it will call the provided callback with the expression that the directive contains.

The following example creates a `@datetime($var)` directive which formats a given `$var`, which should be an instance of `DateTime`:

```php
<?php

namespace App\Providers;

use Illuminate\Support\Facades\Blade;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Perform post-registration booting of services.
     *
     * @return  void
     */
    public function boot()
    {
        Blade::directive('datetime', function ($expression) {
            return "<?php echo ($expression)->format('m/d/Y H:i'); ?>";
        });
    }
     
    /**
     * Register bindings in the container.
     *
     * @return  void
     */
    public function register()
    {
  		//   	
    }
}
```

As we can see, we will chain the `format` method onto whatever expression is passed into the directive. So, in this example, the final PHP generated by this directive will be :

```php
<?php echo ($var)->format('m/d/Y H:i'); ?>
```

> After updating the logic of a Blade directive, you will need to delete all of the cached Blade views. The cached Blade views may be removed using the `view:clear` Artisan command.

##### Custom If Statements

Programming a custom directive is sometimes more complex than necessary when defining simple, custom conditional statements. For that reason, Blade provides a `Blade::if` method which allows you to quickly define custom conditional directives using Closures. For example, let's define a custom conditional that checks the current application environment. We may do this in the `boot` method of our `AppServiceProvider`:

```php
use Illuminate\Support\Facades\Blade;

/**
 * Perform post-registration booting of services,
 *
 * @return  void
 */
public function boot()
{
    Blade::if('env', function($environment) {
        return app()->environment($environment);
    });
}
```

Once the custom conditional has been defined, we can easily use it on our templates:

```php+HTML
@env('local')
	// The application is in the local environment...
@elseenv('testing')
	// The application is in the testing environment...
@else
	// The application is not in the local or testing environment...
@endenv
```



### 5.2 Localization

#### Introduction

Laravel's localization features provide a convenient way to retrieve strings in various languages, allowing you to easily support multiple languages within your application. Language strings are stored in files within the `resourcs/lang` directory. Within this directory there should be a subdirectory for each language supported by the application:

```ini
/resources
	/lang
		/en
			messages.php
		/es
			messages.php
```

All language files return an array of keyed strings. For example:

```php
<?php
return [
    'welcome' => 'Welcome to our application'
];
```

##### Configuring The Locale

The default language for your application is stored in the `config/app.php` configuration file. You may modify this value to suit the needs of your application. You may also change the active language at runtime using the `setLocale` method on the `App` facade:

```php
Route::get('welcome/{locale}', function ($locale) {
    App::setLocale($locale);
});
```

You may configure a "fallcack language", which will be used when the active language does not contain a given translation string. Like the default language, the fallback language is also configured in the `config/app.php` configuration file:

```php
'fallback_locale' => 'en',
```

###### Determining The current locale

You may use the `getLocale` and `isLocale` methods on the `App` facade to determine the current locale or check if the locale is a given value:

```php
$locale = App::getLocale();
if(App::isLocale('en')) {
    //
}
```

#### Defining Translation Strings

##### Using short keys

Typically, translation strings are stored in files within the `resources/lang` directory. Within this directory there should be a subdirectory for each language supported by the application:

```ini
/resources
	/lang
		/en
			messages.php
		/es
			messages.php
```

All language files return an array of keyed strings. For example:

```php
<?php
// resources/lang/en/messages.php
return [
    'welcome' => 'Welcome to our application'
]
```

##### Using Translation Strings As Keys

For applications with heavy translation requirements, defining every string with a "short key" can become quickly confusing when referencing them in your views. For this reason, Laravel also provides support for defining translation strings using the "default" translation of the string as the key.

Translation files that use translation strings as keys are stored as JSON files in the `resources/lang` directory. For example, if your application has a Spanish translation, you should create a `resources/lang/es.json` file:

```json
{
    "I love programming.":"Me encanta programmar."
}
```

#### Retrieving Translation Strings

You may retrieve lines from language files using the `__` helper function. The `__` method accepts the file and key of the translation string as its first argument. For example, let's retrieve the `welcome` translation string from the `resources/lang/messages.php` language file:

```php
echo __('messages.welcome');
echo __('I love programming');
```

If you are using the `Blade templating engine`, you may use the `{{ }}` syntax to echo the translation string or use the `@lang` directive:

```php+HTML
{{ __('messages.welcome') }}
@lang('messages.welcome')
```

If the specified translation string doesn't exist, the `__` function will return the translation string key. So ,using the example above, the `__` function would return `messages.welcome` if the translation string doesn't exist.

> The `@lang` directive doesn't escape any output. You are fully responsible for escaping your own output when using this directive.

##### Replacing Parameters In Translation Strings

If you wish, you may define placeholders in your translation strings. All placeholders are prefixed with a `:`. For example, you may define a welcome message with a placeholder name:

```php
'welcome' => 'Welcome, :name',
```

To replace the placeholders when retrieving a translation string, pass an array of replacements as the second argument to the `__` function:

```php
echo __('messages.welcome', ['name' => 'dayle']);
```

If your placeholder contains all capital letters, or only has its first letter capitalized, the translated value will be capitalized accordingly:

```php
'welcome' => 'Welcome, :NAME', 	// Welcome, DAYLE
'goodbye' => 'Goodbye, :NAME', 	// Goodbye, Dayle
```

##### Pluralization

Pluralization is a complex problem, as different languages have a variety of complex rules for pluralization. By using a "pipe" character, you may distinguish singular and plural forms of a string:

```php
'apples' => 'There is one apple|There are many apples',
```

You may even create more complex pluralization rules which specify translation strings for multiple number ranges:

```php
'apples' => '{0}There are none|[1,19] There are some|[20,*] There are many',
```

After defining a translation string that has pluralization options, you may use the `trans_choice` function to retrieve the line for a given "count". In this example, since the count is greater than one, the plural form of the translation string is returned:

```php
echo trans_choice('messages.apples', 10);
```

You may also define placeholder attributes in pluralization strings. These placeholders may be replaced by passing an array as the third argument to the `trans_choice` function:

```php
'minutes_ago' => '{1} :value minute ago|[2,*] :value minutes ago',
echo trans_choice('time.minutes_ago', 5, ['value' => 5]);
```

If you would like to display the integer value that was passed to the `trans_choice` function, you may use the `:count` placeholder:

```php
'apples' => '{0} There are none|{1} There is one|[2,*] There are :count',
```

#### Overriding Package Language Files

Some packages may ship with their own language files. Instead of changing the package's core files to tweak these lines, you may override them by placing files in the `resources/lang/vendor/{package}/{locale}` directory.

So, for example, if you need to override the English translation strings in `message.php` for a package method `skyrim/hearthfire`, you should place a language file at: `resouces/lang/vendor/hearthfire/en/messages.php`. Within this file, you should only define the translation strings you wish to override. Any translation strings you don't override will still be loaded from the package's original language files.

### 5.3 JS & CSS Scaffolding

#### Introduction

while Laravel doesn't dictate which JavaScript or CSS pre-processors you use, it does provide a basic starting point using `Bootstrap` and `Vue` that will be helpful for many applications. By default, Laravel uses `NPM` to install both of these frontend packages.

###### CSS

`Laravel Mix` provides a clean, expressive API over compiling SASS or Less, which are extensions of plain CSS that add variables, mixins, and other powerful features that make working with CSS much more enjoyable. In this document, we will briefly discuss CSS compilation in general, however, you should consult the full `Laravel Mix documentation(Frontend/Compiling Assets)` for more information on compiling SASS or Less.

###### JavaScript

Laravel doesn't require you to use a specific JavaScript framework or library to build your applications. In fact, you don't have to use JavaScript at all. However, Laravel does include some basic scaffolding to make it easier to get started with modern JavaScript using the `Vue` library. Vue provides an expressive API for building robust JavaScript applications using components. As with CSS, we may use Laravel Mix to easily compile JavaScript components into a single, browser-ready JavaScript file.

###### Removing The frontend Scaffolding

If you would like to remove the frontend scaffolding from your application, you may use the `preset` Artisan command. This command, when combined with the `none` option, will remove the Bootstrap and Vue scaffolding from your application, leaving only a blank SASS file and a few common JavaScript utility libraries:

```shell
php artisan preset none
```



#### Writing CSS

Laravel's `package.json` file includes the `bootstrap` package to help you get started prototyping your application's frontend using Bootstrap. However, feel free to add or remove packages from the `package.json` file as needed for your own application. You are not required to use the Bootstrap framework to build your Laravel application - it is provided as a good starting point for those who choose to use it.

Before compiling your CSS, install your project's frontend dependencies using the `Node package manage(npm)`:

```shell
npm install
```

Once the dependencies have been installed using `npm install`, you can compile your SASS files to plain CSS using `Laravel Mix`. The `npm run dev` command will process the instructions in your `webpack.mix.js` file. Typically, your compiled CSS will be placed in the `public/css` directory:

```shell
npm run dev
```

The default `webpack.mix.js` included with Laravel will compile the `resources/sass/app.scss` SASS file. This `app.scss` file imports a file of SASS variables and loads Bootstrap, which provides a good starting point for most applications. Feel free to customize the `app.scss` file however you wish or even use an entirely different pre-processor by `configuring Laravel Mix(Frontend/Compiling Assets)`.

#### Writing JavaScript

All of the JavaScript dependencies required by your application can be found in the `package.json` file in the project's root directory. This file is similar to a `composer.json` file except it specifies JavaScript dependencies instead of PHP dependencies. You can install these dependencies using the `Node package manager(Npm)`:

```shell
npm install
```

> By default, the Laravel `package.json` file includes a few packages such as `vue` and `axios` to help you get started building your JavaScript application. Feel free to add or remov from the `package.json` file as needed for your own application.

Once the packages are installed, you can use the `npm run dev` command to `compile your assets`. Webpack is a module bundle for modern JavaScript applications. When you run the `npm run dev` command, Webpack will execute the instructions in your `webpack.mix.js` file:

```
npm run dev
```

By default, the Laravel `webpack.mix.js` file compiles your SASS and the `resources/js/app.js` file. Within the `app.js` file you may register you Vue components or, if you prefer a different framework, configure your own JavaScript application. Your compiled JavaScript will typically be placed in the `public/js` directory.

> The `app.js` file will load the `resources/js/bootstrap.js` file which bootstraps and configures Vue, Axios, jQuery and all other JavaScript dependencies. If you have additional JavaScript dependencies to configure, you may do so in this file.

##### Writing Vue Components

By default, fresh Laravel applications contain an `ExampleComponent.vue` Vue component located in the `resources/js/components` directory. The `ExampleComponent.vue` file is an example of a `single file Vue component` which defines its JavaScript and HTML template in the same file. Single file components provide a very convenient approach to build JavaScript driven applications. The example component is registered in your `app.js` file:

```javascript
Vue.component(
	'example-component',
    require('./components/ExampleComponent.vue')
);
```

To use the component in your application, you may drop it into one of your HTML templates. For example, after running the `make:auth` Artisan command to scaffold your application's authentication and registration screens, you could drop the component into the `home.blade.php` Blade template:

```javascript
@extends('layouts.app')
@section('content')
	<example-component></example-component>
@endsection
```

> Remember, you should run the `npm run dev` command each time you change a Vue component. Or you may run the `npm run watch` command to monitor and automatically recompile your components each time they are modified.

If you are interested in learning more about writing Vue components, you should read the `Vue document(https://vuejs.org/guide)`, which provides a thorough, easy-to-read overview of the entire vue framework.

##### Using React

If your prefer to use React to build your JavaScript application, Laravel makes it a cinch to swap the Vue scaffolding with React scaffolding. On any fresh Laravel application, you may use the `preset` command with the `react` option:

```shell
php artisan preset react
```

This single command will remove the Vue scaffolding and replace it with React scaffolding, including an example component.



### 5.4 Compiling Assets

#### Introduction

`Laravel Mix` provides a fluent API for defining Webpack build steps for your Laravel application using several common CSS and JavaScript pre-processors. Through simple method chaining, you can fluently define your asset pipeline. For example:

```javascript
mix.js('resources/js/app.js', 'public/js')
	.sass('resources/sass/app.scss', 'public/css');
```

If you've ever been confused and overwhelmed about getting started with webpack and asset compilation, you will love Laravel Mix. However, you aren't required to use it while developing your application; you are free to use any asset pipeline tool you wish, or even none at all.

#### Installation & Setup

###### Installation & setup

Before triggering Mix, you must first ensure that Node.js and NPM are installed on your machine.

```shell
node -v
npm -v
```

By default Laravel Homestead includes everything you need; However if you aren't use vagrant, then you can easily install the latest version of Node and NPM using simple graphical installers from their download page.

###### Laravel Mix

The only remaining step is to install Laravel Mix. Within a fresh installation of Laravel, you'll find a `package.json` file in the root of your directory structure. The default `package.json` file includes everything you need to get started. Think of this like your `composer.json` file, except it defines Node dependencies instead of PHP. You may install the dependencies it references by running:

```shell
npm install
```



#### Running Mix

Mix is a configuration layer on top of `Webpack`, so to run your Mix tasks you only need too execute one of the NPM scripts that is included with the default Laravel `package.json` file:

```shell
// Run all mix tasks
npm run dev
// Run all mix tasks and minify output
npm run production
```

###### Watching assets for changes

The `npm run watch` command will continue running in your terminal and watch all relevant files for changes. Webpack will then automatically recompile your assets when it detects a change:

```shell
npm run watch
```

You may find that in certain environments Webpack isn't updating when your files change. If this is the case on your system, consider using the `watch-poll` command:

```shell
npm run watch-poll
```



#### Working with Stylesheets

The `webpack.mix.js` file is your entry point for all asset compilation. Think of it as a light configuration wrapper around Webpack. Mix tasks can be chained together to define exactly how your assets should be compiled.

##### Less

The `less` method may be used to compile `Less` into CSS. Let's compile our primary `app.less` file to `public/css/app.css`.

```javascript
mix.less('resources/less/app.less', 'public/css');
```

Multiple calls to the `less` method may be used to compile multiple files:

```javascript
Mix.less('resources/less/app.less', 'public/css')
	.less('resources/less/admin.less', 'public/css');
```

If you wish to customize the file name of the compiled CSS, you may pass a full file path as the second argument to the `less` method:

```javascript
mix.less('resources/less/app.less', 'public/stylesheets/styles.css');
```

If you need to override the `underlying Less plug-in options`, you may pass an object as the third argument to `mix.less()`:

```javascript
mix.less('resources/less/app.less', 'public/css', {
    strictMath: true
});
```

##### SASS

The `sass` method allows you to compile `Sass` into CSS. You may use the method like so:

```javascript
mix.sass('resources/sass/app.scss', 'public/css');
```

Again, like the `less` method, you may compile multiple Sass files into their own respective CSS  files and even customize the output directory of the resulting CSS:

```javascript
mix.sass('resources/sass/app.sass', 'public/css')
	.sass('resources/sass/admin.sass', 'public/vss/admin');
```

Additional `Node-sass plug-in options` may be provided as the third argument.

```javascript
mix.sass('resources/sass/app.sass', 'public/css', {
    precision:5
});
```

##### Stylus

Similar to Less and Sass, the `stylus` method allows you to compile `stylus` into CSS:

```javascript
mix.stylus('resources/stylus/app.styl', 'public/css');
```

You may also install additional Stylus plug-ins, such as `Rupture`. First, install the plug-in iin question through NPM(nom install rupture) and then require it in your call to `mix.stylus()`:

```javascript
mix.stylus('resources/stylus/app.styl', 'public/css', {
    use: [
        require('rupture')()
    ]
});
```

##### PostCSS

`PostCSS`, a powerful tool for transforming your CSS, is included with Laravel Mix out of the box. By default, Mix leverages the popular `Autoprefixer` plug-in to automatically apply all necessary CSS3 vendor prefixes. However, you're free to add any additional plug-ins that are appropriate for your application. First, install the desired plug-in through NPM and then reference it in your `webpack.mix.js` file:

```javascript
mix.sass('resources/sass/app.scss', 'public/css')
    .option({
        postCss: [
            require('postcss-css-variables')()
        ]
	});
```

##### Plain CSS

If you would just like to concatenate some plain CSS stylesheets into a single file, you may use the `styles` method.

```javascript
mix.styles([
    'oublic/css/vendor/normalize.css',
    'public/css/vendor/videojs.css'
], 'public/css/all.css');
```

##### URL Processing

Because Laravel Mix is built on top of Webpack, it's important to understand a few Webpack concepts. For CSS compilation, Webpack will rewrite and optimize any `url()` calls within your stylesheets. While this might initially sound strange, it's an incredibly powerful piece of functionality. Imagine that we want to compile Sass that includes a relative URL to an image:

```javascript
.example {
    background: url('../images/example.png');
}
```

> Absolute paths for any given `url()` will be excluded from URL-rewriting. For example, `url('/images/thing.png')` or `url('http://example.com/images/thing.png')` won't be modified.

By default, Laravel Mix and Webpack will find `example.png`, copy it to your `public/images` folder, and then rewrite the `url()` within your generated stylesheet. As such, your compiled CSS will be:

```javascript
.example {
    background: url('/images/example.png');
}
```

As useful as this feature may be, it's possible that your existing folder structure is already configured in a way you like. If this is the case, you may disable `url()` rewriting like so:

```javascript
mix.sass('resources/app/app.scss', 'public/vss')
    .options({
    	processCssUrls: false
	});
```

With this addition to your `webpack.mix.js` file, Mix will no longer match any `url()` or copy assets to your public directory. In other words, the compiled CSS will look just loke how you originally typed it:

```javascript
.example {
    background: url('../images/thing.png');
}
```

##### Source Maps

Though disabled by default, source maps may be activated by calling the `mix.sourceMaps()` method in your `webpack.mix.js` file. Though it comes with a compile/performance cost, this will provide extra debugging information to your browser's developer tools when using compiled assets.

```javascript
mix.js('resources/js/app.js', 'public/js')
	.sourceMaps();
```



#### Working with JavaScript

Mix provides several features to help you work with your JavaScript files, such as compiling ECMAScript 2015, module bundling, minification, and concatenating plain JavaScript files. Even better, this all works seamlessly, without requiring an ounce of custom configuration:

```javascript
mix.js('resources/js/app.js', 'public/js');
```

With this single line of code, you may now take advantage of:

- ES2015 syntax.
- Modules
- Compilation of `.vue` files.
- Minification for production environments.

##### Vendor Extraction

One potential downside to bundling all application-specific JavaScript with your vendor libraries is that it makes long-term caching more difficult. For example, a single update to your application code will force the browser to re-download all of your vendor libraries even if they haven't changed.

If you intend to make frequent updates to your application's JavaScript, you should consider extracting all of your vendor libraries into their own file. THis way, a change to your application code will not affect the caching of your large `vendor.js` file. Mix's `extract` method makes this a breeze:

```javascript
mix.js('resources/js/app.js', 'public/js')
	.extract(['vue'])
```

The `extract` method accepts an array of all libraries or modules that you wish to extract into a `vendor.js` file. Using the above snippet as an example, Mix will generate the following files:

- `public/js/manifest.js`: The Webpack manifest runtime
- `public/js/vendor.js`: Your vendor libraries
- `public/js/app.js`: Your application code

To avoid JavaScript errors, be sure to load these files in the proper order:

```javascript
<script src="/js/manifest.js"></script>
<script src="/js/vendor.js"></script>
<script src="/js/app.js"></script>
```

##### React

Mix can automatically install the Babel plug-ins necessary for React support. To get started, replace your `mix.js()` call with `mix.react()`:

```javascript
mix.react('resources/js/app.jsx', 'public/js');
```

Behind the scenes, Mix will download and include the appropriate `babel-preset-react` Babel plug-in.

##### Vanilla JS

Similar to combining stylesheets with `mix.styles()`, you may also combine and minify any number of JavaScript files with the `scripts()` method:

```javascript
mix.scripts([
    'public/js/admin.js',
    'public/js/dashboard.js',
], 'public/js/all.js');
```

The option is particularly useful for legacy projects where you don't require Webpack compilation for your JavaScript.

> A slight variation of `mix.scripts()` is `mix.babel()`. Its method signature is identical to `scripts`; however, the concatenated file will receive Babel compilation, which translates any ES2015 code to vanilla JavaScript that all browsers will understand.

##### Custom Webpack configuration

Behind the scenes, Laravel Mix references a pre-configured `webpack.config.js` file to get you up and running as quickly as possible. Occasionally, you may need to manually modify this file. You might have a special loader or plug-in that needs to be referenced, or maybe you prefer to use Stylus instead of Sass In such instances, you have two choices:

###### Merging custom configuration

Mix provides a useful `webpackConfig` method that allows you to merge any short Webpack configuration overrides. This is a particularly appealing choice, as it doesn't require you to copy and maintain your own copy of the `webpack.config.js` file. The `webpackConfig` method accepts an object, which should contain any `Webpack-specific configuration` that you wish to apply.

```javascript
mix.webpackConfig({
    resolve: [
        modules: [
            path.resolve(__dirname, 'vendor/laravel/spark/resources/assets/js')
        ]
    ]
});
```

###### Custom configuration files

If you would like to completely customize your Webpack configuration, copy the `node_modules/laravel-mix/setup/webpack.config.js` file to your projects's root directory. Next, point all of the `--config` references in your `package.json` file to the newly copied configuration file. If you choose to take this approach to customization, any future upstream updates to Mix's `webpack.config.js` must be manually merged into your customized file.

#### Copying files & Directories

The `copy` method may be used to copy files and directories to new locations. This can be useful when a particular asset within your `node_modules` directory needs to be relocated to your `public` folder.

```javascript
mix.copy('node_modules/foo/bar.css', 'public/css/bar.css');
```

When copying a directory, the `copy` method will flattern the directory's structure. To maintain the directory's original structure, you should use the `copyDirectory` method instead.

```javascript
mix.copyDirectory('resources/img', 'public/img');
```

#### Versioning/Cache Busting

Many developers suffix their compiled assets with a timestamp or unique token to force browsers to load the fresh assets instead of serving stale copies of the code. Mix can handle this for you using the `version` method.

The `version` method will automatically append a unique hash to the filenames of all compiled files, allowing for more convenient cache busting:

```javascript
mix.js('resources/js/app.js', 'public/js')
	.version();
```

After generating the versioned file, you won't know the exact file name. So, you should use Laravel's global `mix` function within your `views` to load the appropriately hashed asset. The `mix` function will automatically determine the current name of the hashed file:

```javascript
<script src="{{ mix('/js/app.js') }}"></script>
```

Because versioned files are usually unnecessary in development, you may instruct the versioning process to only run during `npm run production`:

```javascript
mix.js('resources/js/app.js', 'public/js');
if(mix.inProduction()) {
    mix.version();
}
```

#### Browsersync Reloading

`BrowserSync` can automatically monitor your files for changes, and inject your changes into the browser without requiring a manual refresh. You may enable support by calling the `mix.browserSync()` method:

```javascript
mix.browserSync('mydomain.test');
// or..

// https://browsersync.io.docs/options
mix.browserSync({
    proxy: 'mydomain.test'
});
```

You may pass either a string(proxy) or object (BrowserSync settings) to this method. Next, start Webpack's dev server using the `npm run watch` command. Now, when you modfy a script or PHP file, watch as the browser instantly refreshes the page to reflect your changes.

#### Environment variables

You may inject environment variables into Mix by prefixing a key in your `.env` file with `MIX_`:

```ini
MIX_SENTRY_DSN_PUBLIC=http://example.com
```

After the variable has been defined in your `.env` file, you may access via the `process.env` object. If the value changes while you are running a `watch` task, you will need to restart the task:

```ini
process.env.MIX_SENTRY_DSN_PUBLIC
```

#### Notifications

When a available, Mix will automatically display OS notifications for each bundle. This will give you instant feedback, as to whether the compilation was successful or not. However, there may be instances when you would prefer to disable these notifications. One such example might be triggering Mix on your production server. Notifications may be deactivated, via the `disableNotifications` method.

```javascript
mix.disableNotifications();
```



## 6.Security

### 6.1 Authentication

### 6.2 API Authentication

### 6.3 Authorization

### 6.4 Email Verification

### 6.5 Encryption

### 6.6 Hashing

### 6.7 Password Reset

## 7.Digging Deeper

## 8.Database

### 8.1 Getting Started

### 8.2 Query Builder

### 8.3 Pagination

### 8.4 Migrations

### 8.5 Seeding

### 8.6 Redis

## 9.Eloquent ORM

### 9.1 Getting Started

### 9.2 Relationships

### 9.3 Collections

### 9.4 Mutators

### 9.5 API Resources

### 9.6 Serialization

## 10.Testing

### 10.1 Getting Started

### 10.2 HTTP Tests

### 10.3 Console Tests

### 10.4 Browser Tests

### 10.5 Database

### 10.6 Mocking
