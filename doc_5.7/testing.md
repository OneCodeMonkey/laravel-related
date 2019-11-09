# Testing

## Getting Started

### Introduction

Laravel is built with testing in mind. In fact, support for testing with PHPUnit is included out of the  box and a `phpunit.xml` file is already set up for our application. The framework also ships with convenient helper methods that allow you to expressively test applications.

By default, our application's `tests` directory contains two directories: `Feature` and `Unit`. Unit tests are tests that focus on a very small, isolated portion of our code. In fact, most unit tests probably focus on a single method. Feature tests may test a larger portion of our code, including how several objects interact with each other or even a full HTTP request to a JSON endpoint.

An `ExampleTest.php` file is provided in both the `Feature` and `Unit` test directories. After installing a new Laravel application, run `phpunit` on the command line to run our tests.

### Environment

When running tests via `phpunit`, Laravel will automatically set the configuration environment to `testing` because of the environment variables defined in the `phpunit.xml` file. Laravel also automatically configures the session and cache to the `array` driver while testing, meaning no session or cache data will be persisted while testing.

You are free to define other testing environment configuration values as necessary. The `testing` environment variables may be configured in the `phpunit.xml` file, but make sure to clear our configuration cache using the `config:clear` Artisan command before running our tests!

In addition, we may create a `.env.testing` file in the root of our project. This file will override the `.env` file when running PHPUnit tests or executing Artisan commands with the `--env=testing` option.

### Creating & Running Tests

To create a new test case, use the `make:test` Artisan command:

```shell
// Create a test in the Feature directory...
php artisan make:test UserTest
// Create a test in the Unit directory...
php artisan make:test UserTest --unit
```

Once the test has been generated, we may define test methods as we normally would using PHPUnit. To run our tests, execute the `phpunit` command from our terminal:

```php
<?php
    
namespace Tests\Unit;

use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;

class ExampleTest extends TestCase
{
    /**
     * A basic test example.
     *
     * @return  void
     */
    public function testBasicTest()
    {
        $this->assertTrue(true);
    }
}
```



## HTTP Tests

### Introduction

Laravel provides a very fluent API for making HTTP request to our application and examining the output. For example, take a look at the test defined below:

```php
<?php
    
namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithoutMiddleware;

class ExampleTest extends TestCase
{
    /**
     * A basic test example.
     *
     * @return  void
     */
    public function testBasicTest()
    {
        $response = $this->get('/');
        $response->assertStatus(200);
    }
}
```

The `get` method makes a `GET` request into the application, while the `assertStatus` method asserts that the returned response should have the given HTTP status code. In addition to this simple assertion, Laravel also contains a variety of assertions for inspecting the response headers, content, JSON structure, and more.

#### Customizing Request Headers

We may use the `withHeaders` method to customize the request's headers before it is sent to the application. This allows you to add any custom headers we would like to the request:

```php
<?php
    
class ExampleTest extends TestCase
{
    /**
     * A basic functional test example.
     *
     * @return  void
     */
    public function testBasicExample()
    {
        $response = $this->withHeaders([
            'X-Header' => '{$Value}',
        ])->json('POST', '/user', ['name' => 'Bob']);
        $response
            ->assertStatus(201)
            ->assertJson([
                'created' => true,
            ]);
    }
}
```

> The CSRF middleware is automatically disabled when running tests.

### Session/Authentication

Laravel provides several helpers for working with the session during HTTP testing. First, we may set the session data to a given array using the `withSession` method. This is useful for loading the session with data before issuing a request to our application:

```php
<?php
    
class ExampleTest extends TestCase
{
    public function testApplication()
    {
		$response = $this->withSession(['foo' => 'bar'])->get('/');
    }
}
```

One common use of the session for maintaining state for the authenticated user. The `actingAs` helper method provides a simple way to authenticate a given user as the current user. For example, we may use a **model factory** to generate and authenticate a user:

```php
<?php
    
use App\User;

class ExampleTest extends TestCase
{
    public function testApplication()
    {
		$user = factory(User::class)->create();
        $response = $this->actingAs($user)
            ->withSession(['foo' => 'bar'])
            ->get('/');
    }
}
```

We may also specify which guard should be used to authenticate the given user by passing the guard name as the second argument to the `actingAs` method:

```php
$this->actingAs($user, 'api')
```



### Testing JSON APIs

Laravel also provides several helpers for testing JSON APIs and their responses. For example, the `json`, `get`, `post`, `put`, `patch`, and `delete` methods may be used to issue requests with various HTTP verbs. We may also easily pass data and headers to these methods. To get started, let's write a test to make a `POST` request to `/user` and assert that the expected data was returned:

```php
<?php
    
class ExampleTest extends TestCase
{
    /**
     * A basic functional test example.
     *
     * @return  void
     */
    public function testBasicExample()
    {
		$response = $this->json('POST', '/user', ['name' => 'Bob']);
        $response
            ->assertStatus(201)
            ->assertJson([
                'created' => true,
            ]);
    }
}
```

> The `assertJson` method converts the response to an array and utilizes `PHPUnit::assertArraySubset` to verify that the given array exists within the JSON response returned by the application. So, if there are other properties in the JSON response, this test will still pass as long as the given fragment is present.

#### Verifying An Exact JSON Match

If we would like to verify that the given array is an **exact** match for the JSON returned by the application, we should use the `assertExactJson` method:

```php
<?php
    
class ExampleTest extends TestCase
{
    /**
     * A basic functional test example.
     *
     * @return  void
     */
    public function testBasicExample()
    {
		$response = $this->json('POST', '/user', ['name' => 'Bob']);
        $response
            ->assertStatus(201)
            ->assertExactJson([
                'created' => true,
            ]);
    }
}
```



### Testing File Uploads

The `Illuminate\Http\UploadedFile` class provides a `fake` method which may be used to generate dummy files or images for testing. This, combined with the `Storage` facade's `fake` method greatly simplifies the testing of file uploads. For example, we may combine these two features to easily test an avatar upload form:

```php
<?php
    
namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithoutMiddleware;

class ExampleTest extends TestCase
{
    public function testAvatarUpload()
    {
        Storage::fake('avatars');
        $file = UploadedFile::fake()->image('avatar.jpg');
        $response = $this->json('POST', '/avatar', [
            'avatar' => $file,
        ]);
        
        // Assert the file was stored...
        Storage::disk('avatars')->assertExists($file->hasName());
        // Assert a file doesn't exists...
        Storage::disk('avatars')->assertMissing('missing.jpg');
    }
}
```

###### Fake File Customization

When creating files using the `fake` method, we may specify the width, height, and size of the image in order to better test our validation rules:

```php
UploadedFile::fake()->image('avatar.jpg', $width, $height)->size(100);
```

In addition to creating images, we may create files of any other type using the `create` method:

```php
UploadedFile::fake()->create('document.pdf', $sizeInKilobytes);
```

### Available Assertions

#### Response Assertions

Laravel provides a variety of custom assertion methods for our **PHPUnit** tests. These assertions may be accessed on the response that is returned from the `json`, `get`, `post`, `put`, and `delete` test methods:

| assertCookie                      | assertOk                      |
| --------------------------------- | ----------------------------- |
| assertCookieExpired               | assertPlainCookie             |
| assertCookieNotExpired            | assertRedirect                |
| assertCookieMissing               | assertSee                     |
| assertDontSee                     | assertSeeInOrder              |
| assertDontSeeText                 | assertSeeText                 |
| assertExactJson                   | assertSeeTestInOrder          |
| assertForbidden                   | assertSessionHas              |
| assertHeader                      | assertSessionHasAll           |
| assertHeaderMissing               | assertSessionHasErrors        |
| assertJson                        | assertSessionHasErrorsIn      |
| assertJsonCount                   | assertSessionHasNoErrors      |
| assertJsonFragment                | assertSessionDoesntHaveErrors |
| assertJsonMissing                 | assertSessionMissing          |
| assertJsonMissingExact            | assertStatus                  |
| assertJsonMissingValidationErrors | assertSuccessful              |
| assertJsonStructure               | assertViewHas                 |
| assertJsonValidationErrors        | assertViewHasAll              |
| assertLocation                    | assertViewIs                  |
| assertNotFound                    | assertViewMissing             |



#### Authentication Assertions

Laravel also provides a variety of authentication related assertions for our **PHPUnit** tests:

| METHOD                                                       | DESCRIPTION                                    |
| ------------------------------------------------------------ | ---------------------------------------------- |
| `$this->assertAuthencated($guard = null)`                    | Assert that the user is authenticated.         |
| `$this->assertGuest($guard = null);`                         | Assert that the user is not authenticated.     |
| `$this->assertAuthenticatedAs($guard = null)`                | Assert that the given user is authenticated.   |
| `$this->assertCredentials(array $credentials, $guard = null)` | Assert that the given credentials are valid.   |
| `$this->assertInvalidCredentials(array $credentials, $guard = null)` | Assert that the given credentials are invalid. |



## Console Tests

### Introduction

In addition to simplifying HTTP testing, Laravel provides a simple API for testing console applications that ask for user input.

### Expecting Input/Output

Laravel allows us to easily "mock" user input for our console commands using the `expectsQuestion` method. In additon, we may specify the exit code and test that we expect to be output by the console command using the `assertExitCode` and `expectsOutput` methods. For example, consider the following console command:

```php
Artisan::command('question', function () {
    $name = $this->ask('what is your name?');
    $language = $this->choice('Which language do you program in?', [
        'PHP',
        'Ruby',
        'Python'
    ]);
    $this->line('Your name is ' . $name . ' and you program in ' . $language . '.');
});
```

We may test this command with the following test which utilizes the `expectQuestion`, `expectsOutput` and `assertExitCode` methods:

```php
/**
 * Test a console command.
 *
 * @return  void
 */
public function test_console_command()
{
	$this->artisan('question')
        ->expectsQuestion('What is your name?', 'Bob Hany')
        ->expectsQuestion('Which language do you program in?', 'PHP')
        ->expectsOutput('Your name is Bob Hany and you program in PHP.')
        ->assertExitCode(0);
}
```



## Browser Tests

## Database Tests

### Introduction

Laravel provides a variety of helpful tools to make it easier to test your database driven applications. First, you may use the `assertDatabaseHas` helper to assert that data exists in the database matching a given set of criteria. For example, if you would like to verify that there is a record in the `user` table with the `email` value of `Bob@mydomain.com`, you can do the following:

```php
public function testDatabase()
{
    // Make call to application...
    $this->assertDatabaseHas('users', [
        'email' => 'Bob@mydomain.com'
    ]);
}
```

You can also use the `assertDatabaseMissing` helper to assert that data does not exist in the database.

The `assertDatabaseHas` method and other helpers like it are for convenience. You are free to use any of PHPUnit's built-in assertion methods to supplement your tests.

### Generating Factories

To create a factory, use the `make:factory` :

```shell
php artisan make:factory PostFactory
```

The new factory will be placed in your `database/factories` directory.

The `--model` option may be used to indicate the name of the model created by the factory. This option will pre-fill the generated factory file with the given model:

```shell
php artisan make:factory PostFactory --model=Post
```

### Resetting The Database After Each Test

It is often useful to reset your database after each test so that data from a previous test doesn't interfere with subsequent tests. The `RefreshDatabase` trait takes the most optimal approach to migrating your test database depending on if you are using an in-memory database or a traditional database. Use the trait on your test class and everything will be handled for you:

```php
<?php
    
namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithoutMiddleware;

class ExampleTest extends TestCase
{
    use RefreshDatabase;
    /**
     * A basic functional test example.
     *
     * @return void
     */
    public function testBasicExample()
    {
        $response = $this->get('/');
        // ...
    }
}
```



### Writing Factories

When testing, you may need to insert a few records into your database before executing your test, instead of manually specifying the value of each column when you create this test data, Laravel allows you to define a default set of attributes for each of your **Eloquent models** using model factories. To get started, take a look at the `database/factories/UserFactory.php` file in your application. Out of the box, this file contains one factory definition:

```php
use Illuminate\Support\Str;
use Faker\Generator as Faker;
$factory->define(App\User::class, function (Faker $faker) {
    return [
        'name' => $faker->name,
        'email' => $faker->unique()->safeEmail,
        'email_verified_at' => now(),
        'password' => 'XXXX',
        'remember_token' => Str::random(10),
    ];
});
```

Within the Closure, which serves as the factory definition, you may return the default test values of all attributes on the model. The Closure will receive an instance of the `Faker` PHP library, which allows you to conveniently generate various kinds of random data for testing.

You may also create additional factory files for each model for better organization. For example, you could create `UserFactory.php` and `CommentFactory.php` files within your `database/factories` directory. All of the files within the `factories` directory will automatically be loaded by Laravel.

> You can set the Faker locale by adding a `faker_locale` option to your `config/app.php` configuration file.

#### Factory States

State allow you to define discrete modifications that can be applied to your model factories in any combination. For example, your `User` model might have a `delinquent` state that modifies one of its default attribute values. You may define your state transformations using the `state` method. For simple states, you may pass an array of attribute modifications:

```php
$factory->state(App\User::class, 'delinquent', [
    'account_status' => 'delinquent',
]);
```

If your state requires calculation or a `$faker` instance, you may use a Closure to calculate the state's attribute modifications:

```php
$factory->state(App\User::class, 'address', function ($faker) {
    return [
        'address' => $faker->address,
    ];
});
```



#### Factory Callbacks

Factory callbacks are registered using the `afterMaking` and `afterCreating` methods, and allow you to perform additional tasks after making or creating a model. For example, you may use callbacks to relate additional models to the created model:

```php
$factory->afterMaking(App\User::class, function ($user, $faker) {
    // ...
});
$factory->afterCreating(App\User::class, function ($user, $faker) {
    $user->accounts()->save(factory(App\Account::class)->make());
});
```

You may also define callbacks for **factory's states**:

```php
$factory->afterMakingState(App\User::class, 'delinquent',function ($user, $faker) {
    // ...
});
$factory->afterCreatingState(App\User::class, 'delinquent', function ($user, $faker) {
    // ...
});
```

### Using Factories

#### Creating Models

Once you have defined your factories, you may use the global `factory` function in your tests or seed files to generate model instances. So, let's take a look at a few examples of creating models. First, we'll use the `make` method to create models but not save them to the database:

```php
public function testDatabse()
{
    $user = factory(App\User::class)->make();
    // User model in tests...
}
```

You may also create a Collection of many models or create models of a given type:

```php
// Create three App\User instances...
$users = factory(App\User::class, 3)->make();
```

###### Applying States

You may also apply any of your **states** to the models. If you would like to apply multiple state transformations to the models, you should specify the name of each state you would like to apply:

```php
$users = factory(App\User::class, 5)->states('delinquent')->make();
$users = factory(App\User::class, 5)->states('premium', 'delinquent')->make();
```

###### Overriding Attributes

If you would like to override some of the default values of your models, you may pass an array of values to the `make` method. Only the specified values will be replaced while the rest of the values remain set to their default values as specified by the factory:

```php
$user = factory(App\User::class)->make([
    'name' => 'Bob',
]);
```



#### Persisting Models

The `create` method not only creates the model instances but also saves them to the database using Eloquent's `save` method:

```php
public function testDatabase()
{
    // Create a single App\User instance ...
    $user = factory(App\User::class)->create();
    // Create three App\User instances...
    $users = factory(App\User::class, 3)->create();
    // User model in tests...
}
```

You may override attributes on the model by passing an array to the `create` method:

```php
$user = factory(App\User::class)->create([
    'name' => 'Bob'
]);
```



#### Relationships

In this example, we'll attach a relation to some created models. When using the `create` method to create multiple models, an Eloquent **collection instance** is returned, allowing you to use any of the convenient functions provided by the collection, such as `each`:

```php
$users = factory(App\User::class, 3)
    ->create()
    ->each(function ($user) {
        $user->posts()->save(factory(App\Post::class)->make());
    });
```

###### Relations & Attribute Closures

You may also attach relationships to models using Closure attributes in your factory definitions. For example, if you would like to create a new `User` instance when creating a `Post`, you may do the following:

```php
$factory->define(App\Post::class, function ($faker) {
    return [
        'title' => $faker->title,
        'content' => $faker->paragraph,
        'user_id' => function () {
            return factory(App\User::class)->create()->id;
        }
    ];
});
```

These Closures also receive the evaluated attribute array of the factory that defines them:

```php
$factory->define(App\Post::class, function ($faker) {
    return [
        'title' => $faker->title,
        'content' => $faker->paragraph,
        'user_id' => function () {
            return factory(App\User::class)->create()->id;
        },
        'user_type' => function (array $post) {
            return App\User::find($post['user_id'])->type;
        },
    ];
});
```



### Available Assertions

Laravel provides several database assertions for your **PHPUnit** tests:

| METHOD                                               | DESCRIPTION                                    |
| ---------------------------------------------------- | ---------------------------------------------- |
| `$this->assertDatabaseHas($table, array $data);`     | Assert whether a table has the specified data. |
| `$this->assertDatabaseMissing($table, array $data);` | opposite to `assertDatabaseHas`                |
| `$this->assertSoftDeleted($table, array $data);`     | assert whether the given data is soft-deleted. |



## Mocking

### Introduction

When testing Laravel applications, you may wish to "mock" certain aspects of your application so they are not actually executed during a given test. For example, when testing a controller that dispatches an event, you may wish to mock the event listeners so they are not actually executed during the test. This allows you to only test the controller's HTTP response without worrying about the execution of the event listeners, since the event listeners can be tested in their own test case.

Laravel provides helpers for mocking events, jobs, and facades out of the box. These helpers primarily provide a convenience layer over Mockery so you don't have to manually make complicated Mockery method calls. You can also use **Mockery** or PHPUnit to create your own mocks or spies.

### Bus Fake

As an alternative to mocking, you may use the `Bus` facade's `fake` method to prevent jobs from being dispatched. When using fakes, assertions are made after the code under test is executed:

```php
<?php
    
namespace Tests\Feature;

use Tests\TestCase;
use App\Jobs\ShipOrder;
use Illuminate\Support\Facades\Bus;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithoutMiddleware;

class ExampleTest extends TestCase
{
	public function testOrderShipping()
    {
		Bus::fake();
        //..
        Bus::assertDispatched(ShipOrder::class, function ($job) use ($order) {
            return $job->order->id === $order->id;
        });
        
        // Assert a job wasn't dispatched...
        Bus::assertNotDispatched(AnotherJob::class);
    }
}
```



### Event Fake

As an alternative to mocking, you may use the `Event` facade's `fake` method to prevent all event listeners from executing. You may then assert that events were dispatched and even inspect the data they received. When using fakes, assertions are made after the code under test is executed:

```php
<?php
    
namespace Tests\Feature;

use Tests\TestCase;
use App\Events\OrderShipped;
use App\Events\OrderFailedToShip;
use Illuminate\Support\Facades\Event;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithoutMiddleware;

class ExampleTest extends TestCase
{
    /**
     * Test order shipping.
     *
     */
    public function testOrderShipping()
    {
        Event::fake();
        // Perform order shipping...
        Event::assertDispatched(OrderShipped::class, function ($e) use ($order) {
            return $e->order->id === $order->id;
        });
        // Assert an event was dispatched twice...
        Event::assertDispatched(OrderShipped::class, 2);
        // Assert an event was not dispatched...
        Event::assertNotDispatched(OrderFailedToShip::class);
    }
}
```

> After calling `Event::fake()`, no event listeners will be executed. So, if your tests use model factories that rely on events, such as creating a UUID during a model's `creating` event, you should call `Event::fake()` after using your factories.

###### Faking A subset of events

If you only want to fake event listeners for a specific set of events, you may pass them to the `fake` or `fakeFor` method:

```php
/**
 * Test order process.
 */
public function testOrderProcess()
{
    Event::fake([
        OrderCreated::class,
    ]);
    $order = factory(Order::class)->create();
    Event::assertDispatched(OrderCreated::class);
    // Other events are dispatched as normal...
    $order->update([
        // ...
    ]);
}
```

#### Scoped Event Fakes

If you only want to fake event listeners for a portion of your test, you may use the `fakeFor` method:

```php
<?php
    
namespace Tests\Feature;

use App\Order;
use Tests\TestCase;
use App\Events\OrderCreated;
use Illuminate\Support\Facades\Event;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithoutMiddleware;

class ExampleTest extends TestCase
{
    /**
     * Test order process.
     */
    public function testOrderProcess()
    {
        $order = Event::fakeFor(function () {
            $order = factory(Order::class)->create();
            Event::assertDispatched(OrderCreated::class);
            return $order;
        });
        // Events are dispatched as normal and observers will run
        $order->update([
            // ...
        ]);
    }
}
```



### Mail Fake

You may use the `Mail` facade's `fake` method to prevent mail from being sent. You may then assert that **mailables** were sent to users and even inspect the data they received. When using fakes, assertions are made after the code under test is executed:

```php
<?php
    
namespace Tests\Feature;

use Tests\TestCase;
use App\Mail\OrderShipped;
use Illuminate\Support\Facades\Mail;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithoutMiddleware;

class ExampleTest extends TestCase
{
    public function testOrderShipping()
    {
        Mail::fake();
        // Assert that no mailables were sent...
        Mail::assertNothingSent();
        
        // Perform order shipping...
        
        Mail::assertSent(OrderShipped::class, function ($mail) use ($order) {
            return $mail->order->id === $order->id;
        });
        
        // Assert a message was sent to the given users...
        Mail::assertSent(OrderShipped::class, function ($mail) use ($user) {
            return $mail->hasTo($user->email) && $mail->hasCc('...') && $mail->hasBcc('...');
        });
        
        // Assert a mailable was sent twice...
        Mail::assertSent(OrderShipped::class, 2);
        
        // Assert a mailable was not sent...
        Mail::assertNotSent(AnotherMailable::class);
    }
}
```

If you are queueing mailables for delivery in the background, you should use the `assertQueued` method instead of `assertSent`:

```php
Mail::assertQueued(...);
Mail::assertNotQueued(...);
```



### Notification Fake

You may use the `Notification` facade's `fake` method to prevent notifications from being sent. You may then assert that **notifications** were sent to users and even inspect the data they received. When using fakes, assertions are made after the code under test is executed:

```php
<?php
    
namespace Tests\Feature;

use Tests\TestCase;
use App\Notifications\OrderShipped;
use Illuminate\Support\Facades\Notification;
use Illuminate\Notifications\AnonymousNotifiable;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithoutMiddleware;

class ExampleTest extends TestCase
{
    public function testOrderShipping()
    {
        Notification::fake();
        // Assert that no notifications were sent...
        Notification::assertNothingSent();
        // Perform order shipping...
        
        Notification::assertSentTo(
        	$user,
            OrderShipped::class,
            function ($notification, $channels) use ($order) {
                return $notification->order->id === $order->id;
            }
        );
        
        // Assert a notification was sent to the given users...
        Notification::assertSentTo(
        	[$user], OrderShipped::class
        );
        
        // Assert a notification was not sent...
        Notification::assertNotSentTo(
        	[$user], AnotherNotification::class
        );
        
        // Assert a notification was sent via Notification::route() method...
        Notification::assertSentTo(
        	new AnonymousNotifiable, OrderShipped::class
        );
    }
}
```



### Queue Fake

As an alternative to mocking, you may use the `Queue` facade's `fake` method to prevent jobs from being queued. You may then assert that jobs were pushed to the queue and even inspect the data they received. When using fakes, assertions are made after the code under test is executed:

```php
<?php
    
namespace Tests\Feature;

use Tests\TestCase;
use App\Jobs\ShipOrder;
use Illuminate\Support\Facades\Queue;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithoutMiddleware;

class ExampleTest extends TestCase
{
    public function testOrderShipping()
    {
        Queue::fake();
        
        // Assert that no jobs were pushed...
        Queue::assertNothingPushed();
        
        // Perform order shipping...
        // ...
        
        Queue::assertPushed(ShipOrder::class, function ($job) use ($order) {
            return $job->order->id === $order->id;
        });
        
        // Assert a job was pushed to a given queue...
        Queue::assertPushedOn('queue-name', ShipOrder::class);
        
        // Assert a job was pushed twice...
        Queue::assertPushed(ShipOrder::class, 2);
        
        // Assert a job was not pushed...
        Queue::assertNotPushed(AnotherJob::class);
        
        // Assert a job was pushed with a specific chain...
        Queue::assertPushedWithChain(ShipOrder::class, [
            AnotherJob::class,
            FinalJob::class,
        ]);
    }
}
```



### Storage Fake

The `Storage` facade's `fake` method allows you to easily generate a fake disk that, combined with the file generation utilities of the `UploadedFile` class, greatly simplifies the testing of file uploads. For example:

```php
<?php
    
namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithoutMiddleware;

class ExampleTest extends TestCase
{
    public function testAvatarUpload()
    {
        Storage::fake('avatars');
        $response = $this->json('POST', '/avatar', [
            'avatar' => UploadedFile::fake()->image('avatar.jpg')
        ]);
        
        // Assert the file was stored...
        Storage::disk('avatar')->assertExists('avatar.jpg');
        
        // Assert a file does not exist...
        Storage::disk('avatars')->assertMissing('missing.jpg');
    }
}
```



### Facades

Unlike traditional static method calls, **facades** may be mocked. This provides a great advantage over traditional static methods and grants you the same testability you would have if you were using dependency injection. When testing, you may often want to mock a call to a Laravel facade in one of your controllers. For example, consider the following controller action:

```php
<?php
    
namespace App\Http\Controllers;

use Illuminate\Support\Facades\Cache;

class UserController extends Controller
{
    /**
     * Show a list of all users of the application.
     *
     * @return  Response
     */
    public function index()
    {
        $value = Cache::get('key');
        //
    }
}
```

We can mock the call to the `Cache` facade by using the `shouldReceive` method, which will return an instance of a **Mockery** mock. Since facades are actually resolved and managed by the Laravel **service container**, they have much more testability than a typical static class. For example, let's mock our call to the `Cache` facade's `get` method:

```php
<?php
    
namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Support\Facades\Cache;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithoutMiddleware;

class UserControllerTest extends TestCase
{
    public function testGetIndex()
    {
        Cache::shouldReceive('get')
            ->once()
            ->with('key')
            ->andReturn('value');
        
       	$response = $this->get('/users');
        
        // ...
    }
}
```

> You should not mock the `Request` facade. Instead, pass the input you desire into the HTTP helper methods such as `get` and `post` when running your test. Likewise, instead of mocking the `Config` facade, call the `Config::set` method in your tests.