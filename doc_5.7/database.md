## 8.Database

### 8.1 Getting Started

#### Introduction

Laravel makes interacting with databases extremely simple across a variety of database backends using either raw SQL, the **fluent query builder**, and the **Eloquent ORM**. Currently, Laravel supports four databases.

- MySQL
- PostgreSQL
- SQLite
- SQL Server

##### Configuration

The database configuration for your application is located at `config/database.php`. In this file you may define all of your database connections, as well as specify which connection should be used by default. Examples for most of the supported database systems are provided in this file.

By default, Laravel's sample **environment configuration** is ready to use with **Laravel Homestead**, which is a convenient virtual machine for doing Laravel development on your local machine. You are free to modify this configuration as needed for your local database.

###### SQLite Configuration

After creating a new SQLite database using a command such as `touch database/database.sqlite`, we can easily configure our environment variables to point to this newly created database by using the database's absolute path:

```php
DB_CONNECTION=sqlite
DB_DATABASE=/absolute/path/to/database.sqlite
```

To enable foreign key constraints for SQLite connections, we should add the `foreign_key_constraints` option to our `config/database.php` configuration file:

```php
'sqlite' => [
    //...
    
    'foreign_key_constraints' => true,
],
```

##### Read & Write Connections

Sometimes we may wish to use one database connection for SELECT statements, and another for INSERT, UPDATE, and DELETE statements. Laravel makes this a breeze(小菜一碟，= a cinch), and the proper connections will always be used whether we are using raw queries, the query builder, or the Eloquent ORM.

To see how read/write connections should be configured, let's look at this example:

```php
'mysql' => [
    'read' => [
        'host' => ['192.168.1.1'],
    ],
    'write' => [
        'host' => ['192.168.1.2'],
    ],
    'sticky' => true,
    'driver' => 'mysql',
    'database' => 'database',
    'username' => 'root',
    'password' => '',
    'charset' => 'utf8mb4',
    'collation' => 'utf8mb4_unicode_ci',
    'prefix' => '',
],
```

Note that three keys have been added to the configuration array: `read`, `write` and `sticky`. The `read` and `write` keys have array values containing a single key: `host`. The rest of the database options for the `read` and `write` connections will be merged from the main `mysql` array.

You only need to place items in the `read` and `write` arrays if you wish to override the values from the main array. So, in this case, `192.168.1.1` will be used as the host for the "read" connection, while `192.168.1.2` will be used for the "write" connection. The database credentials, prefix, character set, and all other options in the main `mysql` array will be shared across both connections.

###### The `sticky` option

The `sticky` option is an *optional* value that can be used to allow the immediate reading of records that have been written to the database during the current request cycle. If the `sticky` option is enabled and a "write" operation has been performed against the database during the current request cycle, any further "read" operations will use the "write" connection. This ensures that any data written during the request cycle can be immediately read back from the database during that same request. It is up to you to decide if this is the desired behavior for your application.

##### Using Multiple Database Connections

When using multiple connections, we may access each connection via the `connection` method on the `DB` facade. The `name` passed to the `connection` method should correspond to one of the connections listed in our `config/database.php` configuration file:

```php
$users = DB::connection('foo')->select(...);
```

We may also access the raw, underlying PDO instance, using the `getPdo` method on a connection instance:

```php
$pdo = DB::connection()->getPdo();
```



#### Running Raw SQL Queries

Once we have configured our database connection, we may run queries using the `DB` facade. The `DB` facade provides methods for each type of query: `select`, `update`, `insert`, `delete` and `statement`.

###### Running a select query

To run a basic query, you may use the `select` method on the `DB` facade:

```php
<?php
    
namespace App\Http\Controllers;

use Illuminate\Support\Facades\DB;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    /**
     * Show a list of all of the application's users.
     *
     * @return  Response
     */
    public function index()
    {
        $users = DB::select('select * from users where active = ?', [1]);
        return view('user.index', ['users' => $users]);
    }
}
```

The first argument passed to the `select` method is the raw SQL query, while the second argument is any parameter bindings that need to be bound to the query. Typically, these are the values of the `where` clause constraints. Parameter binding provides protection against SQL injection.

The `select` method will always return an `array` of results. Each result within the array will be a PHP `stdClass` object, allowing you to access the values of the results:

```php
foreach($users as $user) {
    echo $user->name;
}
```

###### Uisng Named Bindings

Instead of using `?` to represent your parameter bindings, you may execute a query using named bindings:

```php
$results = DB::select('select * from users where id = :id', ['id' => 1]);
```

###### Running An Insert Statement

To execute an `insert` statement, you may use the `insert` method on the `DB` facade. Like `select`, this method takes the raw SQL query as its first argument and bindings as its second argument:

```php
DB::insert('insert into users(id, name) values(?, ?)', [1, 'Bob']);
```

###### Running An update Statement

The `update` method should be used to update existing records in the database. The number of rows affected by the statement will be returned:

```php
$affected = DB::update("update users set votes = 100 where name = ?", ['John']);
```

###### Running A Delete Statement

The `delete` method should be used to delete records from the database. Like `update`, the number of rows affected will be returned:

```php
$deleted = DB::delete('delete from users');
```

###### Running A general statement

Some database statements do not return any value. For these types of operations, you may use the `statement` method on the `DB` facade:

```php
DB::statement('drop table users');
```



##### Listening For Query Events

If you would like to receive each SQL query executed by your application, you may use the `listen` method. This method is useful for logging queries or debugging. You may register your query listener in a **service container**:

```php
<?php
    
namespace App\Providers;

use Illuminate\Support\Facades\DB;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return  void
     */
    public function boot()
    {
        DB::listen(function ($query) {
            // $query->sql
            // $query->bindings
            // $query->time
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

#### Database Transactions

You may use the `transaction` method on the `DB` facade to run a set of operations within a database transaction. If an exception is thrown within the transaction `Closure`, the transaction with automatically be rolled back. If the `Closure` executes successfully, the transaction will automatically be commited. You don't need to worry about manually rolling back or committing while using the `transaction` method:

```php
DB::transaction(function () {
    DB::table('users')->table(['votes' => 1]);
    DB::table('posts')->delete();
})
```

###### Handing Deadlocks

The `transaction` method accepts an optional second argument which defines the number of times a transaction should be reattempted when a deadlock occurs. Once these attempts have been exhausted, an exception will be thrown: (当死锁发生时，超过指定次数的重试后会抛出异常)

```php
DB::transaction(function () {
    DB::table('users')->update(['votes' => 1]);
    DB::table('posts')->delete();
}, 5);
```

###### Manually Using Transactions

If you would like to begin a transaction manually and have complete control over rollbacks and commits, you may use the `beginTransaction` method on the `DB` facade:

```php
DB::beginTransaction();
```

You can rollback the transaction via the `rollBack` method:

```php
DB::rollback();
```

Lastly, you can commit a transaction via the `commit` method:

```php
DB::commit();
```

> The `DB` facade's transaction methods control the transactions for both the **query builder** and **Eloquent ORM**.

### 8.2 Query Builder

#### Introduction

Laravel's database query builder provides a convenient, fluent interface to creating and running database queries. It can be used to perform most database operations in your application and works on all supported database systems.

The Laravel query builder uses PDO parameter binding to protect your application against SQL injection attacks. These is no need to clean strings being passed as bindings.

#### Retrieving Results

You may use the `table` method on the `DB` facade to begin a query. The `table` method returns a fluent query builder instance for the given table, allowing you to chain more constraints onto the query and then finally get the results using the `get` method:

```php
<?php
    
namespace App\Http\Controllers;

use Illuminate\Support\Facades\DB;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    /**
     * Show a list of all of the application's users.
     *
     * @return  Response
     */
    public function index()
    {
        $users = DB::table('users')->get();
        return view('user.index', ['users' => $users]);
    }
}
```

The `get` method returns an `Illuminate\Support\Collection` containing the results where each result is an instance of the PHP `stdClass` object. You may access each column's value by accessing the column as a property of the object:

```php
foreach($users as $user) {
    echo $user->name;
}
```

###### Retrieving A Single Raw/ Column From a table

If you just need to retrieve a single row from the database table, you may use the `first` method. This method will return a single `stdClass` object:

```php
$user = DB::table('users')->where('name', 'John')->first();
echo $user->name;
```

If you don't even need an entire row, you may extract a single value from a record using the `value` method. This method will return the value of the column directly:

```php
$email = DB::table('users')->where('name', 'John')->value('email');
```

###### Retrieving a list of column values

If you would like to retrieve a Collection containing the values of a single column, you may use the `pluck` method. In this example, we'll retrieve a Collection of role titles:

```php
$titles = DB::table('roles')->pluck('title');

foreach($titles as $title) {
    echo $title;
}
```

You may also specify a custom key column for the returned Collection:

```php
$roles = DB::table('roles')->pluck('title', 'name');
foreach($roles as $name => $title) {
    echo $title;
}
```

##### 

##### Chunking Results

If you need to work with thousands of database records, consider using the `chunk` method. This method retrieves a small chunk of the results at a time and feeds each chunk into a `Closure` for processing. This method is very useful for writing **Artisan commands** that process thousands of records. For example, let's work with the entire `users` table in chunks of 100 records at a time:

```php
DB::table('users')->orderBy('id')->chunk(100, function ($users) {
    foreach($users as $user) {
        //
    }
});
```

You may stop further chunks(停止chunk继续遍历) from being processed by returning `false` from the `Closure`:

```php
DB::table('users')->orderBy('id')->chunk(100, function ($users) {
    // ...
    return false;
});
```

If you are updating database records while chunking results, your chunk results could change in unexpected ways. So, when updating records while chunking, it is always best to use the `chunkById` method instead. This method will automatically paginate the results based on the record's primary key:(chunk 自更新问题)

```php
DB::table('users')->where('active', false)->chunkById(100, function ($users) {
    foreach($users as $user) {
        DB::table('users')
            ->where('id', $user->id)
            ->update(['active' => true]);
    }
});
```

> When updating or deleting records inside the chunk callback, any changes to the primary key or foreign keys could affect the chunk query. This could potentially result in records not being included in the chunked results.



##### Aggregates

The query builder also provides a variety of aggregate methods such as `count`, `max`, `min`, `avg`, `sum`. You may call any of these methods after constructing your query:

```php
$users = DB::table('users')->count();
$price = DB::table('orders')->max('price');
```

You may combine these methods with other clauses:

```php
$price = DB::table('orders')->where('finalized', 1)->avg('price');
```

###### Determining If Records Exist

Instead of using the `count` method to determine if any records exist that match your query's constraints, you may use the `exists` and `doesntExist` methods:

```php
return DB::table('orders')->where('finalized', 1)->exists();
return DB::table('orders')->where('finalized', 1)->doesntExist();
```



#### Selects

###### Specifying A Select Clause

You may not always want to select all columns from a database table. Using the `select` method, you can specify a custom `select` clause for the query:

```php
$users = DB::table('users')->select('name', 'email as user_email')->get();
```

The `distinct` method allows you to force the query to return distinct results:

```php
$users = DB::table('users')->distinct()->get();
```

If you already have a query builder instance and you wish to add a column to its existing select clause, you may use the `addSelect` method:

```php
$query = DB::table('users')->select('name');
$users = $query->addSelect('age')->get();
```



#### Raw Expressions

Sometimes you may need to use a raw expression in a query. To create a raw expression, you may use the `DB::raw` method:

```php
$users = DB::table('users')
    ->select(DB::raw('count(*) as user_count, status'))
    ->where('status', '<>', 1)
    ->groupBy('status')
    ->get();
```

> Raw statements will be injected into the query as strings, so you should be extremely careful to not create SQL injection vulnerabilities.

##### Raw Methods

Instead of using `DB::raw`, you may also use the following methods to insert a raw expression into various parts of your query.

1.`selectRaw`

The `selectRaw` method can be used in place of `select(DB::raw(...))`. This method accepts an optional array of bindings as its second argument.

```php
$orders = DB::table('orders')
    ->selectRaw('price * ? as price_with_tax', [1.0810])
    ->get();
```

2.`whereRaw`/`orWhereRaw`

The `whereRaw` and `orWhereRaw` methods can be used to inject a raw `where` clause into your query. These methods accept an optional array of bindings as their second argument:

```php
$orders = DB::table('orders')
    ->whereRaw('price > IF(state = "TX", ?, 100)', [200])
    ->get();
```

3.`havingRaw`/`orHavingRaw`

The `havingRaw` and `orHavingRaw` methods may be used to set a raw string as the value of the `having` clause. These methods accept an optional array of bindings as their second argument:

```php
$orders = DB::table('orders')
    ->select('department', DB::raw('SUM(price) as total_sales'))
    ->groupBy('department')
    ->havingRaw('SUM(price) > ?', [2500])
    ->get();
```

4.`orderByRaw`

The `orderByRaw` method may be used to set a raw string as the value of the `order by`clause:

```php
$orders = DB::table('orders')
    ->orderByRaw('updated_at - created_at DESC')
    ->get();
```



#### Joins

###### Inner Join Clause

The query builder may also be used to write join statements. To perform a basic "inner join", you may use the `join` method on a query builder instance. The first argument passed to the `join` method is the name of the table you need to join to, while the remaining arguments specify the column constraints for the join. You can even join to multiple tables in a single query:

```php
$users = DB::table('users')
    ->join('contacts', 'users.id', '=', 'contacts.user_id')
    ->join('orders', 'users.id', '=', 'orders.user_id')
    ->select('users.*', 'contacts.phone', 'orders.price')
    ->get();
```

###### Left Join / Right Join Clause

If you would like to perform a "left join" or "right join" instead of an "inner join", use the `leftJoin` or `rightJoin` methods. These methods have the same signature as the `join` method:

```php
$users = DB::table('users')
    ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
    ->get();
$users = DB::table('users')
    ->rightJoin('posts', 'users.id', '=', 'posts.user_id')
    ->get();
```

###### Cross Join Clause

To perform a "cross join" use the `crossJoin` method with the name of the table you wish to cross join to. Cross joins generate a cartesian product between the first table and the joined table:

```php
$users = DB::table('sizes')
    ->crossJoin('colours')
    ->get();
```

###### Advanced Join Clauses

You may also specify more advanced join clauses. To get started, pass a `Closure` as the second argument into the `join` method. The `Closure` will receive a `JoinClause` object which allows you to specify constraints on the `join` clause:

```php
DB::table('users')
    ->join('contacts', function ($join) {
        $join->on('users.id', '=', 'contacts.user_id')->orOn(...);
    })
    ->get();
```

If you would like to use a "where" style clause on your joins, you may use the `where` and `orWhere` methods on a join. Instead of comparing two columns, these methods will compare the column against a value:

```php
DB::table('users')
    ->join('contacts', function ($join) {
        $join->on('users.id', '=', 'contacts.user_id')
            ->where('contacts.user_id', '>', 5);
    })
    ->get();
```

###### Sub-query joins

You may use the `joinSub`, `leftJoinSub` and `rightJoinSub` methods to join a query to a sub-query. Each of these methods receive three arguments: the sub-query, its table alias, and a Closure that defines the related columns:

```php
$latestPosts = DB::table('posts')
    ->select('user_id', DB::raw('MAX(create_at) as last_post_created_at'))
    ->where('is_published', true)
    ->groupBy('user_id');
$users = DB::table('users')
    ->joinSub($latestPosts, 'latest_posts', function ($join) {
        $join->on('users.id', '=', 'latest_posts.user_id');
    })
    ->get();
```



#### Unions

The query builder also provides a quick way to "union" two queries together. For example, you may create an initial query and use the `union` method to union it with a second query:

```php
$first = DB::table('users')
    ->whereNull('first_name');

$users = DB::table('users')
    ->whereNull('last_name')
    ->union($first)
    ->get();
```

> The `unionAll` method is also available and has the same method signature as `union`.
>

#### Where Clauses

###### Simple Where Clauses

You may use the `where` method on a query builder instance to add `where` clauses to the query. The most basic call to `where` requires three arguments. The first argument is the name of the column. The second argument is an operator, which can be any of the database's supported operators. Finally, the third argument is the value to evaluate against the column.

For example, here is a query that verifies the value of the "votes" column is equal to 100:

```php
$users = DB::table('users')->where('votes', '=', 100)->get();
```

For convenience, if you want to verify that a column is equal to a given value, you may pass the value directly as the second argument to the `where` method:

```php
$users = DB::table('users')->where('votes', 100)->get();
```

You may use a variety of other operators when writing a `where` clause:

```php
$users = DB::table('users')
	->where('votes', '>=', 100)
    ->get();
$users = DB::table('users')
    ->where('votes', '<>', 100)
    ->get();
$users = DB::table('users')
    ->where('name', 'like', 'T%')
    ->get();
```

You may also pass an array of conditions to the `where` function:

```php
$users = DB::table('users')->where([
    ['status', '=', '1'],
    ['subscribed', '<>', '1'],
])->get();
```

###### Or Statement

You may chain where constraints together as well as add `or` clauses to the query. The `orWhere` method accepts the same arguments as the `where` method:

```php
$users = DB::table('users')
    ->where('votes', '<>', 100)
    ->orWhere('name', 'John')
    ->get();
```

##### Addition Where Clauses

###### whereBetween

The `whereBetween` method verifies that a column's value is between two values:

```php
$users = DB::table('users')
    ->whereBetween('votes', [1, 100])
    ->get();
```

###### whereNotBetween

The `whereNotBetween` method verifies that a column's value lies outside of two values:

```php
$users = DB::table('users')
    ->whereNotBetween('votes', [1, 100])
    ->get();
```

###### whereIn/whereNotIn

The `whereIn` method verifies that a given column's value is contained within the given array:

```php
$users = DB::table('users')
    ->whereIn('id', [1, 2, 3])
    ->get();
```

The `whereNotIn` method verifies that the given column's value is not contained in the given array:

```php
$users = DB::table('users')
    ->whereNotIn('id', [1, 2, 3])
    ->get();
```

###### whereNull/whereNotNull

The `whereNull` method verifies that the value of the given column is `NULL`:

```php
$users = DB::table('users')
    ->whereNull('updated_at')
    ->get();
```

The `whereNotNull` method verifies that the column's value is not `NULL`:

```php
$users = DB::table('users')
    ->whereNotNull('updated_at')
    ->get();
```

###### whereDate/whereMonth/whereDay/whereYear/whereTime

The `whereDate` method may be used to compare a column's value against a date:

```php
$users = DB::table('users')
    ->whereDate('created_at', '2016-12-31')
    ->get();
```

The `whereMonth` method may be used to compare a column's value against a specific month of a year:

```php
$users = DB::table('users')
    ->whereMonth('created_at', '12')
    ->get();
```

The `whereDay` method may be used to compare a column's value against a specific day of a month:

```php
$users = DB::table('users')
    ->whereDay('created_at', '31')
    ->get();
```

The `whereYead` method may be used to compare a column's value against a specific year:

```php
$users = DB::table('users')
    ->whereYear('created_at', '2019')
    ->get();
```

The `whereTime` method may be used to compare a column's value against a specific time:

```php
$users = DB::table('users')
    ->whereTime('created_at', '=', '11:20:45')
    ->get();
```

###### whereColumn

The `whereColumn` method may be used to verify that two columns are equal:

```php
$users = DB::table('users')
    ->whereColumn('first_name', 'last_name')
    ->get();
```

You may also pass a comparison operator to the method:

```php
$users = DB::table('users')
    ->whereColumn('updated_at', '>', 'created_at')
    ->get();
```

The `whereColumn` method can also be passed an array of multiple conditions. These conditions will be joined using the `and` operator:

```php
$users = DB::table('users')
    ->whereColumn([
        ['first_name', '=', 'last_name'],
        ['updated_at', '>', 'created_at'],
    ])->get();
```



##### Parameter Grouping

Sometimes you may need to create more advanced where clauses such as "where exists" clauses or nested parameter groupings. The Laravel query builder can handle these as well. To get started, let's look at an example of grouping constraints within parenthesis:

```php
DB::table('users')
    ->where('name', '=', 'Bob')
    ->where(function ($query) {
        $query->where('votes', '>', 100)
            ->orWhere('title', '=', 'Admin');
    })
    ->get();
```

As you can see, passing a `Closure` into the `where` method instructs the query builder to begin a constraint group. The `Closure` will receive a query builder instance which you can use to set the constraints that should be contained within the parenthesis group. The example above will produce the following SQL:

```sql
select * from users where name = 'Bob' and (votes > 100 or title = 'Admin')
```

> You should always group `orWhere` calls in order to avoid unexpected behavior when global scopes are applied.



##### Where Exists Clauses

The `whereExists` method allows you to write `where exists` SQL clauses. The `whereExists` method accepts a `Closure` argument, which will receive a query builder instance allowing you to define the query that should be placed inside of the "exists" clause:

```php
DB::table('users')
    ->whereExists(function ($query) {
        $query->select(DB::raw(1))
            ->from('orders')
            ->whereRaw('orders.user_id = users.id');
    })->get();
```

The query above will produce the following SQL:

```sql
select * from users
where exists(
	select 1 from orders where orders.user_id = users.id
)
```



##### JSON where clause

Laravel also supports querying JSON column types on databases that provide support for JSON column types. Currently, this includes MySQL 5.7, PostgreSQL, SQL Server 2016, and SQLite 3.9.0(within the **JSON1 extension**). To query a JSON column, see the `->` operator:

```php
$users = DB:table('users')
    ->where('options->language', 'en')
    ->get();
$users = DB::table('users')
    ->where('preferences->dining->meal', 'salad')
    ->get();
```

You may use `whereJsonContains` to query JSON arrays(not supported on SQLite):

```php
$users = DB::table('users')
    ->whereJsonContains('options->languages', 'en')
    ->get();
```

MySQL and PostgreSQL support `whereJsonContains` with multiple values:

```php
$users = DB::table('users')
    ->whereJsonContains('options->languages', ['en', 'de'])
    ->get();
```

You may use `whereJsonLength` to query JSON arrays by their length:

```php
$users = DB::table('users')
    ->whereJsonLength('options->languages', 0)
    ->get();
$users = DB::table('users')
    ->whereJsonLength('options->languages', '>', 1)
    ->get();
```

#### Ordering, Grouping, Limit, & Offset

###### orderBy

The `orderBy` method allows you to sort the result of the query by a given column. The first argument to the `orderBy` method should be the column you wish to sort by, while the second argument controls the direction of the sort and may be either `asc` or `desc`:

```php
$users = DB::table('users')
    ->orderBy('name', 'desc')
    ->get();
```

###### latest/oldest

The `latest` and `oldest` methods allow yo to easily order results by date. By default, result will be ordered by the `created_at` column. Or, you may pass the column name that you wish to sort by:

```php
$users = DB::table('users')
    ->latest()
    ->first();
```

###### inRandomOrder

The `inRandomOrder` method may be used to sort the query results randomly. For example, you may use this method to fetch a random user:

```php
$randomUser = DB::table('users')
    ->inRandomOrder()
    ->first();
```

###### groupBy/having

The `groupBy` and `having` methods may be used to group the query results. The `having` method's signature is similar to that of the `where` method:

```php
$users = DB::table('users')
    ->groupBy('account_id')
    ->having('account_id', '>', 100)
    ->get();
```

You may pass multiple arguments to the `groupBy` method to group by multiple columns:

```php
$users = DB::table('users')
    ->groupBy('first_name', 'status')
    ->having('account_id', '>', 100)
    ->get();
```

For more advanced `having` statements, see the `havingRaw` method.

###### skip/take

To limit the number of results returned from the query, or to skip a given number of results in the query, you may use the `skip` and `take` methods.

```php
$users = DB::table('users')->skip(10)->take(5)->get();
```

Alternatively, you may use the `limit` and `offset` methods:

```php
$users = DB::table('users')
    ->offset(10)
    ->limit(5)
    ->get();
```



#### Conditional Clauses

Sometimes you may want clauses to apply to a query only when something else is true. For instance, you may only want to apply a `where` statement if a given input value is present on the incoming request. You may accomplish this using the `when` method:

```php
$role = $request->input('role');
$users = DB::table('users')
    ->when($role, function ($query, $role) {
        return $query->where('role_id', $role);
    })->get();
```

The `when` method only executes the given Closure when the first parameter is `true`. If the first parameter is `false`, the Closure will not be executed.

You may pass another Closure as the third parameter to the `when` method. This Closure will execute if the first parameter evaluates as `false`. To illustrate how this feature may be used, we will use it to configure the default sorting of a query:

```php
$sortBy = null;
$users = DB::tables('users')
    ->where($sortBy, function ($query, $sortBy) {
        return $query->orderBy($sortBy);
    }, function ($query) {
        return $query->orderBy('name');
    })->get();
```



#### Inserts

The query builder also provides an `insert` method for inserting records into the database table. The `insert` method accepts an array of column names and values:

```php
DB::table('users')
    ->insert([
        'email' => 'Bob@test.com',
        'votes' => 0,
    ]);
```

You may even insert several records into the table with a single call to `insert` by passing an array of arrays. Each array represents a row to be inserted into the table:

```php
$DB::table('users')->insert([
    ['email' => 'Tom@test.com', 'votes' => 0],
    ['email' => 'Jerry@test.com', 'votes' => 1],
]);
```

###### Auto-incrementing IDs

If the table has an auto-incrementing id, use the `insertGetId` method to insert a record and then retrieve the ID:

```php
$id = DB::table('users')->insertGetId([
    'email' => 'Bob@test.com', 'votes' => 0
]);
```

> When using PostgreSQL the `insertGetId` method expects the auto-incrementing column to be named `id`. If you would like to retrieve the ID from a different "sequence", you may pass the column name as the second parameter to the `insertGetId` method.

#### Updates

In addition to inserting records into the database, the query builder can also update existing records using the `update` method. The `update` method, like the `insert` method, accepts an array of column and value pairs containing the columns to be updated. You may constrain the `update` query using `where` clauses:

```php
DB::table('users')
    ->where('id', 1)
    ->update(['votes' => 1]);
```

###### Update Or Insert

Sometimes you may want to update an existing record in the database or create it if no matching record exists. In this scenario, the `updateOrInsert` method may be used. The `updateOrInsert` method accepts two arguments: an array of conditions by which to find the record, and an array of column and value pairs containing the columns to be updated.

The `updateOrInsert` method will first attempt to locate a matching database record using the first argument's column and value pairs. If the record exists, it will be updated with the values in the second argument. If the record can not be found, a new record will be inserted with the merged attributes of both arguments:

```php
DB::table('users')
    ->updateOrInsert(
		['email' => 'Bob@test.com', 'name' => 'Bob'],
    	['votes' => '2']
	);
```



##### Updating JSON Columns

When updating a JSON column, we shou use `->` syntax to access the appropriate key in the JSON object. This operation is only supported on MySQL 5.7+:

```php
DB::table('users')
    ->where('id', 1)
    ->update(['options->enabled' => true]);
```



##### Increment & Decrement

The query builder also privides convenient methods for incrementing or decrementing the value of a given column. This is a shortcut, providing a more expressive and terse interface compared to manually writing the `update` statement.

Both of these methods accept at least one argument: the column to modify. A second argument may optionally be passed to control the amount by which the column should be incremented or decremented:

```php
DB::table('users')->increment('votes');
DB::table('users')->increment('votes', 5);
DB::table('users')->decrement('votes');
DB::table('users')->decrement('votes', 5);
```

We may also specify additional columns to update during the operation:

```php
DB::table('users')->increment('votes', 1, ['name' => 'Bob']);
```

#### Deletes

The query builder may also be used to delete records from the table via the `delete` method. You may constrain `delete` statements by adding `where` clauses before calling the `delete` method:

```php
DB::table('users')->delete();
DB::table('users')->where('votes', '>', 100)->delete();
```

If you wish to truncate the entire table, which will remove all rows and reset the auto-incrementing ID to zero, you may use the `truncate` method:

```php
DB::table('users')->truncate();
```

#### Pessimistic Locking

The query builder also includes a few functions to help you do "pessimistic locking" on your `select` statements. To run the statement with a "shared lock", you may use the `sharedLock` method on a query. A shared lock prevents the selected rows from being modified until your transaction commits:

```php
DB::table('users')->where('votes', '>', 100)->sharedLock()->get();
```

Alternatively, you may use the `lockForUpdate` method. A "for update" lock prevents the rows from being modified or from being selected with another shared lock:

```php
DB::table('users')->where('votes', '>', 100)->lockForUpdate()->get();
```



### 8.3 Pagination

### 8.4 Migrations

### 8.5 Seeding

### 8.6 Redis