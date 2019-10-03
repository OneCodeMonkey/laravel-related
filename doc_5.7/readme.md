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

##### If Statements

##### Switch Statements

##### Loops

##### The Loop Variable

##### Comments

##### PHP

#### Forms

##### CSRF Field

##### Method Field

#### Including Sub-Views

##### Rendering Views For Collections

#### Stacks

#### Service Injection

#### Extending Blade

##### Custom If Statements

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
