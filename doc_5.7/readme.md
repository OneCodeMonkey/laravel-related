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

#### The Root Directory

#### The App Directory

### 2.4 Homestead

[homestead usage](https://laravel.com/docs/5.7/homestead)

### 2.5 Valet

// 用不到

### 2.6 Deployment

#### Introduction

#### Server Configuration

##### Nginx

#### Optimization

##### Autoloader Optimization

##### Optimizing Configuration Loading

##### Optimizing Route Loading

#### Deploying With Forge

## 3.Architecture Concepts

### 3.1 Request LifeCycle

### 3.2 Service Container

### 3.3 Service Providers

### 3.4 Facades

### 3.5 Contracts

## 4.The Basics

### 4.1 Routing

### 4.2 Middleware

### 4.3 CSFR Protection

### 4.4 Controllers

### 4.5 Requests

### 4.6 Responses

### 4.7 Views

### 4.8 URL Generation

### 4.9 Session

### 4.10 Validation

### 4.11 Error Handling

### 4.12 Logging

## 5.Frontend

### 5.1 Blade Templates

### 5.2 Localization

### 5.3 Frontend Scaffolding

### 5.4 Compiling Assets

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

## 11.Official Packages