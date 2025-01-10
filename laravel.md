# Most Asked Laravel Developer Interview Questions (With Answers)

## General Laravel Questions

1. **What is Laravel, and why is it popular?**
   Laravel is a PHP framework that follows the MVC (Model-View-Controller) architectural pattern. It is popular due to its elegant syntax, built-in tools like Eloquent ORM, routing, authentication, and Blade templating, and its large community support.
   
   **Reference:** [Laravel Official Documentation](https://laravel.com/docs)

2. **What are service providers in Laravel, and why are they important?**
   Service providers are the central place to configure and bind services in Laravel's service container. They are responsible for bootstrapping the application's features, like routes, events, and middleware.

   **Reference:** [Service Providers](https://laravel.com/docs/master/providers)

3. **Explain the lifecycle of a Laravel request.**
   - The entry point is `public/index.php`.
   - The request is passed to the HTTP Kernel.
   - The kernel processes middleware.
   - The request is sent to the router to match a route.
   - The matched route executes the controller method, which interacts with models and views.
   - The response is returned to the client.

   **Reference:** [Laravel Request Lifecycle](https://laravel.com/docs/master)

4. **What is Eloquent ORM, and how is it different from raw SQL queries?**
   Eloquent ORM is Laravel's built-in ORM that provides an elegant way to interact with the database using models. Unlike raw SQL queries, Eloquent allows working with database tables as PHP objects, making the code more readable and maintainable.

   **Reference:** [Eloquent ORM](https://laravel.com/docs/master/eloquent)

5. **What are middleware in Laravel? Can you provide an example of how to create and use them?**
   Middleware filters HTTP requests entering your application. For example, authentication middleware checks if a user is logged in before granting access to a route.

   To create middleware: `php artisan make:middleware CheckAge`

   Use middleware in a route:
   ```php
   Route::get('/profile', function () {
       // Your code here
   })->middleware('auth');
   ```

   **Reference:** [Middleware](https://laravel.com/docs/master/middleware)

---

## Routing and Controllers

6. **How does Laravel handle routing?**
   Laravel uses the `routes/web.php` and `routes/api.php` files to define routes. Routes can be defined using closures or controller methods.

   Example:
   ```php
   Route::get('/home', [HomeController::class, 'index']);
   ```

   **Reference:** [Routing](https://laravel.com/docs/master/routing)

7. **What is the difference between `Route::get()` and `Route::post()`?**
   `Route::get()` handles GET requests, typically used to fetch and display data. `Route::post()` handles POST requests, commonly used for submitting forms or creating resources.

   **Reference:** [HTTP Methods](https://laravel.com/docs/master/routing#the-method-field)

8. **What are resource controllers, and how do you use them?**
   Resource controllers provide a way to define CRUD routes automatically. Generate a resource controller:

   ```bash
   php artisan make:controller UserController --resource
   ```

   Register the resource controller in routes:
   ```php
   Route::resource('users', UserController::class);
   ```

   **Reference:** [Resource Controllers](https://laravel.com/docs/master/controllers#resource-controllers)

9. **How can you handle route model binding in Laravel?**
   Route model binding allows you to inject model instances directly into routes based on route parameters. Define it in a controller:
   ```php
   public function show(User $user)
   {
       return $user;
   }
   ```

   **Reference:** [Route Model Binding](https://laravel.com/docs/master/routing#route-model-binding)

10. **How do you redirect routes in Laravel?**
    Use the `redirect()` helper:
    ```php
    Route::get('/old-url', function () {
        return redirect('/new-url');
    });
    ```

    **Reference:** [Redirects](https://laravel.com/docs/master/redirects)

---

## Eloquent ORM and Database

11. **Explain the difference between `hasOne` and `hasMany` relationships in Eloquent.**
    - `hasOne`: Defines a one-to-one relationship. Example: A user has one profile.
    - `hasMany`: Defines a one-to-many relationship. Example: A post has many comments.

    **Reference:** [Eloquent Relationships](https://laravel.com/docs/master/eloquent-relationships)

12. **What is the purpose of database migrations in Laravel?**
    Migrations provide a version control system for your database schema, allowing you to modify and share the database schema easily.

    **Reference:** [Migrations](https://laravel.com/docs/master/migrations)

13. **How do you perform raw SQL queries in Laravel?**
    Use the `DB` facade:
    ```php
    $users = DB::select('SELECT * FROM users');
    ```

    **Reference:** [Raw Queries](https://laravel.com/docs/master/database#running-queries)

14. **How does Laravel handle database seeding?**
    Seeders populate the database with dummy data for testing. Example:
    ```bash
    php artisan make:seeder UserSeeder
    ```

    **Reference:** [Database Seeding](https://laravel.com/docs/master/seeding)

15. **What are model observers in Laravel, and how are they used?**
    Observers handle events like creating, updating, or deleting a model. Example:
    ```bash
    php artisan make:observer UserObserver --model=User
    ```

    Register the observer in a service provider:
    ```php
    User::observe(UserObserver::class);
    ```

    **Reference:** [Model Observers](https://laravel.com/docs/master/eloquent#observers)

---

## Authentication and Authorization

16. **How does Laravel’s built-in authentication work?**
    Laravel provides built-in authentication scaffolding that includes login, registration, and password reset functionalities. Use `php artisan make:auth` (for older versions) or `php artisan ui:auth` (for Laravel UI).

    **Reference:** [Authentication](https://laravel.com/docs/master/authentication)

17. **What is the difference between gates and policies in Laravel?**
    - **Gates:** Define authorization logic in closures. Example:
      ```php
      Gate::define('update-post', function ($user, $post) {
          return $user->id === $post->user_id;
      });
      ```
    - **Policies:** Define authorization logic for a specific model in a dedicated class.

    **Reference:** [Authorization](https://laravel.com/docs/master/authorization)

18. **How would you implement role-based access control (RBAC) in Laravel?**
    Use a package like `spatie/laravel-permission` or implement a custom RBAC by associating roles and permissions with users.

    **Reference:** [Spatie Role Permissions](https://spatie.be/docs/laravel-permission)

19. **What is the purpose of the `Auth` facade in Laravel?**
    The `Auth` facade provides methods to authenticate users and check their authentication state.

    **Reference:** [Auth Facade](https://laravel.com/docs/master/authentication#authenticating-users)

20. **How can you customize Laravel’s default authentication behavior?**
    Modify the `AuthServiceProvider` or override methods in the default authentication controllers.

    **Reference:** [Custom Authentication](https://laravel.com/docs/master/authentication#customizing-authentication)

---

## Blade Templating

21. **What is Blade in Laravel?**
    Blade is Laravel’s templating engine that allows you to write PHP code in views using simple and readable syntax.

    **Reference:** [Blade Templates](https://laravel.com/docs/master/blade)

22. **How can you include one Blade view into another?**
    Use the `@include` directive:
    ```php
    @include('partials.header')
    ```

    **Reference:** [Blade Includes](https://laravel.com/docs/master/blade#including-subviews)

23. **What are Blade components, and how are they useful?**
    Blade components allow you to create reusable UI components. Example:
    ```bash
    php artisan make:component Alert
    ```
    Use the component in views:
    ```php
    <x-alert type="success" message="Task completed!" />
    ```

    **Reference:** [Blade Components](https://laravel.com/docs/master/blade#components)

24. **How do you pass data to Blade views?**
    Use the `with` method:
    ```php
    return view('profile')->with('user', $user);
    ```

    **Reference:** [Passing Data to Views](https://laravel.com/docs/master/views#passing-data-to-views)

25. **What are the differences between `@include`, `@yield`, and `@extends`?**
    - `@include`: Embeds another view.
    - `@yield`: Defines a section that child views can fill.
    - `@extends`: Specifies the parent layout for a child view.

    **Reference:** [Blade Layouts](https://laravel.com/docs/master/blade#extending-layouts)

---

## API Development

26. **How would you create a RESTful API in Laravel?**
    Use `php artisan make:controller ApiController --api` to create an API controller. Define routes in `routes/api.php` and use Eloquent models to handle database operations.

    **Reference:** [Building APIs](https://laravel.com/docs/master/eloquent-resources)

27. **What is Laravel Sanctum, and how is it used?**
    Sanctum provides authentication for SPAs (Single Page Applications) and APIs using tokens. Install it via:
    ```bash
    composer require laravel/sanctum
    ```
    Configure middleware and use the `HasApiTokens` trait in your User model.

    **Reference:** [Sanctum](https://laravel.com/docs/master/sanctum)

28. **How does Laravel handle API rate limiting?**
    Use the `throttle` middleware. Example:
    ```php
    Route::middleware('throttle:60,1')->group(function () {
        Route::get('/api/resource', [ApiController::class, 'index']);
    });
    ```

    **Reference:** [Rate Limiting](https://laravel.com/docs/master/routing#rate-limiting)

29. **How would you return JSON responses in Laravel?**
    Use the `response()->json()` method:
    ```php
    return response()->json(['success' => true]);
    ```

    **Reference:** [JSON Responses](https://laravel.com/docs/master/responses#json-responses)

30. **What is the difference between `json()` and `response()` in Laravel?**
    - `json()`: Shortcut for returning JSON responses.
    - `response()`: More versatile, used for creating various types of responses, including JSON.

    **Reference:** [Responses](https://laravel.com/docs/master/responses)

    # Laravel Developer Interview Questions (31 to 50)

## Advanced Concepts

31. **What are events and listeners in Laravel, and how do you use them?**
    - **Events**: Allow you to decouple various parts of your application by triggering actions when certain conditions occur.
    - **Listeners**: Handle the logic that should execute in response to an event.

    Example:
    ```bash
    php artisan make:event UserRegistered
    php artisan make:listener SendWelcomeEmail --event=UserRegistered
    ```

    Register the event and listener in `EventServiceProvider` and dispatch the event:
    ```php
    event(new UserRegistered($user));
    ```

    **Reference:** [Events and Listeners](https://laravel.com/docs/events)

32. **How can you schedule tasks in Laravel?**
    Use the `schedule` method in `app/Console/Kernel.php` to define scheduled tasks.

    Example:
    ```php
    protected function schedule(Schedule $schedule)
    {
        $schedule->command('emails:send')->daily();
    }
    ```

    Run the scheduler:
    ```bash
    php artisan schedule:run
    ```

    **Reference:** [Task Scheduling](https://laravel.com/docs/scheduling)

33. **What is a queued job in Laravel?**
    A queued job allows you to defer time-intensive tasks to a queue. Use `php artisan make:job` to create a job and dispatch it with `dispatch()`.

    Example:
    ```php
    dispatch(new ProcessOrder($order));
    ```

    **Reference:** [Queues](https://laravel.com/docs/queues)

34. **What is the repository pattern in Laravel?**
    The repository pattern separates business logic from data access logic by abstracting database queries into repository classes.

    **Reference:** [Repository Pattern]([https://laravel.io/articles/repository-pattern-in-laravel](https://medium.com/@soulaimaneyh/laravel-repository-pattern-da4e1e3efc01))

35. **What is the difference between `Session` and `Cache` in Laravel?**
    - **Session**: Stores user-specific data for the duration of the user's session.
    - **Cache**: Temporarily stores data to speed up application performance.

    **Reference:** [Session and Cache](https://laravel.com/docs/session)

---

## Testing

36. **What is Laravel Dusk, and how is it used?**
    Laravel Dusk provides browser automation testing for web applications.

    Install Dusk:
    ```bash
    composer require --dev laravel/dusk
    ```
    Create tests with `php artisan dusk:make TestName` and run them with `php artisan dusk`.

    **Reference:** [Dusk](https://laravel.com/docs/dusk)

37. **How can you test APIs in Laravel?**
    Use Laravel's built-in `TestCase` class with methods like `get()` and `post()` to test API endpoints.

    Example:
    ```php
    $response = $this->get('/api/users');
    $response->assertStatus(200);
    ```

    **Reference:** [HTTP Tests](https://laravel.com/docs/testing#http-tests)

38. **What is the purpose of factories in testing?**
    Factories generate fake data for testing purposes. Define them in `database/factories` and use them in tests:
    ```php
    User::factory()->count(10)->create();
    ```

    **Reference:** [Factories](https://laravel.com/docs/database-testing#writing-factories)

39. **What is the purpose of database transactions in testing?**
    Laravel resets the database after each test by wrapping it in a transaction, ensuring isolation between tests.

    **Reference:** [Database Testing](https://laravel.com/docs/database-testing#resetting-the-database-after-each-test)

40. **How do you mock dependencies in Laravel tests?**
    Use Laravel's `Mockery` library to mock dependencies and focus on specific parts of the code.

    Example:
    ```php
    $mock = Mockery::mock(UserRepository::class);
    $this->app->instance(UserRepository::class, $mock);
    ```

    **Reference:** [Mocking](https://laravel.com/docs/testing#mocking)

---

## Security

41. **How does Laravel protect against SQL injection?**
    Laravel uses parameter binding in queries to prevent SQL injection:
    ```php
    DB::table('users')->where('id', '=', $id)->get();
    ```

    **Reference:** [Query Builder](https://laravel.com/docs/database#query-builder)

42. **What are CSRF tokens, and how does Laravel implement them?**
    CSRF tokens prevent cross-site request forgery attacks. Laravel includes the `VerifyCsrfToken` middleware to validate CSRF tokens in forms.

    **Reference:** [CSRF Protection](https://laravel.com/docs/csrf)

43. **How does Laravel encrypt data?**
    Use the `Crypt` facade to encrypt and decrypt data:
    ```php
    $encrypted = Crypt::encrypt('secret');
    $decrypted = Crypt::decrypt($encrypted);
    ```

    **Reference:** [Encryption](https://laravel.com/docs/encryption)

44. **What is Laravel Passport?**
    Passport provides full OAuth2 server implementation for API authentication.

    Install Passport:
    ```bash
    composer require laravel/passport
    php artisan passport:install
    ```

    **Reference:** [Passport](https://laravel.com/docs/passport)

45. **How can you secure sensitive environment variables?**
    Store sensitive data in the `.env` file, and restrict file access permissions. Never expose `.env` in version control.

    **Reference:** [Configuration](https://laravel.com/docs/configuration)

---

## Miscellaneous

46. **What is the purpose of the `storage` directory in Laravel?**
    The `storage` directory holds logs, compiled Blade templates, file uploads, and cached data.

    **Reference:** [File Storage](https://laravel.com/docs/filesystem)

47. **What are service containers in Laravel?**
    The service container is a powerful tool for managing class dependencies and performing dependency injection.

    **Reference:** [Service Container](https://laravel.com/docs/container)

48. **What is the difference between `view()` and `response()`?**
    - `view()`: Renders a Blade view.
    - `response()`: Returns a custom HTTP response.

    **Reference:** [Responses](https://laravel.com/docs/responses)

49. **How do you handle file uploads in Laravel?**
    Use the `store()` method:
    ```php
    $path = $request->file('avatar')->store('avatars');
    ```

    **Reference:** [File Storage](https://laravel.com/docs/filesystem)

50. **What is the purpose of the `artisan` command in Laravel?**
    Artisan is Laravel's command-line interface for running tasks like migrations, seeding, and creating files.

    **Reference:** [Artisan Console](https://laravel.com/docs/artisan)
# Advanced Laravel Developer Interview Questions (3-5 Years of Experience)

## 51. How do you optimize the performance of a Laravel application in a production environment?

Optimizing performance in Laravel involves several strategies, including:

- **Caching**: Utilize Laravel's caching features (query caching, view caching, and route caching).
- **Database optimization**: Use efficient queries, indexes, and avoid N+1 queries using eager loading.
- **Queue management**: Offload time-consuming tasks to queues to improve response times.
- **Asset optimization**: Use Laravel Mix to minify CSS and JavaScript and serve compressed files.
- **Profiling**: Use tools like Laravel Telescope or Xdebug to identify bottlenecks in your application.

**Reference:** [Performance Optimization](https://laravel.com/docs/optimization)

---

## 52. What is the role of middleware in Laravel, and how can you create custom middleware?

Middleware in Laravel provides a convenient mechanism for filtering HTTP requests entering your application. It allows you to perform tasks such as authentication, logging, and data manipulation before passing the request to the controller. You can create custom middleware by running `php artisan make:middleware <MiddlewareName>` and defining your logic in the `handle` method.

**Reference:** [Middleware Documentation](https://laravel.com/docs/middleware)

---

## 53. How do you implement role-based access control (RBAC) in Laravel?

Role-based access control can be implemented in Laravel by using the built-in Gate and Policy features. You can define roles (e.g., Admin, User) and permissions for different actions. Define your roles and permissions in the database or use packages like `spatie/laravel-permission` to simplify the process.

- Create policies using `php artisan make:policy` to authorize actions based on user roles.
- Use middleware like `can` to check user permissions on specific routes.

**Reference:** [Authorization and Gates](https://laravel.com/docs/authorization)

---

## 54. How do you handle API rate limiting in Laravel?

Laravel provides built-in support for API rate limiting via the `ThrottleRequests` middleware. You can define rate limits for your API routes in the `RouteServiceProvider` or directly in the routes file using the `throttle` middleware. For more advanced scenarios, you can define custom throttle logic using the `RateLimiter` facade.

**Reference:** [API Rate Limiting](https://laravel.com/docs/rate-limiting)

---

## 55. Explain the differences between `find()`, `first()`, and `firstOrFail()` in Laravel Eloquent.

- **`find()`**: Retrieves a record by its primary key. Returns `null` if the record is not found.
- **`first()`**: Retrieves the first record that matches the given condition. Returns `null` if no records are found.
- **`firstOrFail()`**: Similar to `first()`, but throws a `ModelNotFoundException` if no records are found, which is useful for API error handling.

**Reference:** [Eloquent Methods](https://laravel.com/docs/eloquent)

---

## 56. How do you handle file uploads in Laravel?

Laravel provides a simple API for handling file uploads. Files can be uploaded using the `store()` method, and you can configure the storage disk (e.g., local, S3) in the `filesystems.php` configuration. Laravel also supports file validation to ensure that the uploaded files meet specific requirements (e.g., file type, size).

- Example: `$request->file('image')->store('images');`

**Reference:** [File Uploads Documentation](https://laravel.com/docs/filesystem#file-uploads)

---

## 57. What are events and listeners in Laravel, and how do you use them?

Events and listeners allow you to decouple your application’s logic. An event is an action that triggers some behavior, while a listener reacts to the event. You can define events and listeners using `php artisan make:event` and `php artisan make:listener`. Events can be fired manually with `event(new EventName())`.

**Reference:** [Events and Listeners Documentation](https://laravel.com/docs/events)

---

## 58. How do you handle database migrations and versioning in Laravel?

Migrations in Laravel allow you to version control your database schema. You can create and modify database tables using migration files, which can be applied to different environments. Common migration commands include:

- `php artisan migrate`: Apply migrations.
- `php artisan migrate:rollback`: Rollback migrations.
- `php artisan migrate:refresh`: Reset and re-run migrations.

Migrations ensure that your database structure is consistent across different environments and teams.

**Reference:** [Migrations Documentation](https://laravel.com/docs/migrations)

---

## 59. How do you implement AJAX requests in Laravel?

In Laravel, AJAX requests can be handled using JavaScript (e.g., jQuery, Axios) on the client side, while the server side can respond with JSON data. You can define routes to handle AJAX requests and return JSON responses with `response()->json()`.

- Example: `$.ajax({ url: '/example', method: 'GET' }).done(function(data) { console.log(data); });`

**Reference:** [AJAX Requests in Laravel](https://laravel.com/docs/responses)

---

## 60. What is the `with()` method in Eloquent, and how is it different from `load()`?

- **`with()`**: Eager loads relationships on a model before the query is executed. It's typically used in the initial query.
- **`load()`**: Loads relationships after the model has already been retrieved. It’s used for dynamically loading relationships after the query.

**Reference:** [Eager Loading in Laravel](https://laravel.com/docs/eloquent-relationships#eager-loading)

---

## 61. What are "Observers" in Laravel, and when should you use them?

Observers allow you to group model event listeners. Laravel provides built-in observers for events like `created`, `updated`, `deleted`, etc. Instead of defining event listeners in a controller, you can use observers to handle these events in a dedicated class.

- Example: `php artisan make:observer PostObserver --model=Post`

**Reference:** [Observers Documentation](https://laravel.com/docs/eloquent#observers)

---

## 62. How do you manage environment-specific configurations in Laravel?

Laravel provides the `.env` file for managing environment-specific configurations. You can use the `config` function to retrieve these values in your application, e.g., `config('app.env')`. Additionally, you can define different `.env` files for different environments (e.g., `.env.production`, `.env.local`).

**Reference:** [Environment Configuration](https://laravel.com/docs/configuration)

---

## 63. How do you implement logging in Laravel, and what are some best practices?

Laravel provides a powerful logging system using the Monolog library. You can log messages to various channels, including daily log files, syslog, and external services. Use the `Log` facade to log messages at different levels (e.g., `Log::info()`, `Log::error()`). For best practices, log only relevant information, avoid logging sensitive data, and monitor log files in production environments.

**Reference:** [Logging in Laravel](https://laravel.com/docs/logging)

---

## 64. How do you implement API authentication in Laravel?

Laravel offers several methods for API authentication, including:

- **Passport**: OAuth2 server for API authentication.
- **Sanctum**: Lightweight authentication system for SPAs and simple API tokens.
- **JWT (JSON Web Tokens)**: A package like `tymon/jwt-auth` can be used for token-based authentication.

**Reference:** [API Authentication Documentation](https://laravel.com/docs/passport)

# Advanced Laravel Interview Questions (65-75)

## 65. How would you implement real-time features in Laravel?

Laravel provides several approaches for real-time functionality:
- **Laravel Broadcasting**: For broadcasting events to WebSocket connections
- **Laravel Echo**: JavaScript library for subscribing to channels and listening to events
- **Pusher**: Integration with external WebSocket service
- **Laravel WebSockets**: Self-hosted WebSocket server

Example implementation:
```php
class OrderStatusUpdated implements ShouldBroadcast
{
    public $order;

    public function broadcastOn()
    {
        return new PrivateChannel('orders.' . $this->order->id);
    }
}
```

**Reference:** [Laravel Broadcasting](https://laravel.com/docs/broadcasting)

## 66. How do you implement horizontal scaling in Laravel applications?

Key considerations for horizontal scaling:
- Session management using Redis or other distributed cache
- Queue workers configuration for multiple servers
- Load balancer setup
- Filesystem management (using S3 or similar)
- Cache invalidation strategies
- Database replication and sharding

Example Redis session configuration:
```php
'redis' => [
    'driver' => 'redis',
    'connection' => 'default',
    'table' => 'sessions',
    'store' => 'redis',
    'lottery' => [2, 100],
]
```

## 67. Explain Laravel's Service Container and Dependency Injection patterns.

Advanced container bindings:
```php
// Contextual binding
$this->app->when(PhotoController::class)
          ->needs(Filesystem::class)
          ->give(function () {
              return Storage::disk('local');
          });

// Tagged bindings
$this->app->tag([
    VideoProcessor::class,
    AudioProcessor::class,
], 'processors');

// Extending resolved instances
$this->app->extend(Service::class, function ($service, $app) {
    return new DecoratedService($service);
});
```

## 68. How would you implement a robust error handling and monitoring system?

Advanced error handling setup:
```php
class Handler extends ExceptionHandler
{
    public function register()
    {
        $this->reportable(function (CustomException $e) {
            // Log to external service
            \Sentry::captureException($e);
            
            // Custom notification
            Notification::route('slack', 'webhook-url')
                ->notify(new ErrorOccurred($e));
        })->stop();

        $this->renderable(function (ApiException $e) {
            return response()->json([
                'error' => $e->getMessage(),
                'code' => $e->getCode(),
                'context' => $e->getContext(),
            ], $e->getStatusCode());
        });
    }
}
```

## 69. How would you implement a multi-tenant architecture in Laravel?

Key aspects of multi-tenancy:
```php
class TenantScope implements Scope
{
    public function apply(Builder $builder, Model $model)
    {
        $builder->where('tenant_id', tenant()->id);
    }
}

class Tenant extends Model
{
    protected $connection = 'tenant';
    
    public function configure()
    {
        Config::set('database.connections.tenant.database', 
            'tenant_' . $this->id);
        
        DB::purge('tenant');
        DB::reconnect('tenant');
    }
}
```

## 70. Explain advanced Eloquent relationship techniques.

Complex relationship examples:
```php
class Post extends Model
{
    // Polymorphic many-to-many with custom pivot
    public function tags()
    {
        return $this->morphToMany(Tag::class, 'taggable')
                    ->using(CustomPivot::class)
                    ->withPivot(['order', 'added_by'])
                    ->withTimestamps();
    }

    // Has many through with constraints
    public function threadReplies()
    {
        return $this->hasManyThrough(
            Reply::class, 
            Thread::class
        )->where('is_approved', true)
         ->orderBy('created_at', 'desc');
    }
}
```

## 71. How would you implement a robust caching strategy?

Advanced caching implementation:
```php
class ProductService
{
    public function getProductDetails($id)
    {
        return Cache::tags(['products', "product-{$id}"])
            ->remember("product-{$id}", now()->addHours(24), function () use ($id) {
                return Product::with(['categories', 'variants'])
                    ->withCount('reviews')
                    ->findOrFail($id);
            });
    }

    public function invalidateProductCache($id)
    {
        Cache::tags(["product-{$id}"])->flush();
        
        // Implement cache warming
        dispatch(new WarmProductCache($id));
    }
}
```

## 72. How would you implement a rate-limiting system with dynamic rules?

Advanced rate limiting:
```php
class RateLimitingService
{
    public function configureRateLimits()
    {
        RateLimiter::for('api', function (Request $request) {
            return Limit::perMinute(60)->by(optional($request->user())->id ?: $request->ip())
                ->response(function () {
                    return response()->json([
                        'error' => 'Too many requests',
                        'retry_after' => RateLimiter::availableIn($key)
                    ], 429);
                });
        });

        RateLimiter::for('uploads', function (Request $request) {
            return [
                Limit::perMinute(100)->by($request->ip()),
                Limit::perDay(1000)->by($request->user()->id),
            ];
        });
    }
}
```

## 73. How would you implement a job batching system with progress tracking?

Advanced job batching:
```php
class ProcessUserData implements ShouldQueue
{
    public function handle()
    {
        Bus::batch([
            new ProcessUserProfile($this->user),
            new ProcessUserOrders($this->user),
            new ProcessUserAnalytics($this->user)
        ])->then(function (Batch $batch) {
            // All jobs completed
            Event::dispatch(new UserDataProcessed($this->user));
        })->catch(function (Batch $batch, Throwable $e) {
            // First batch job failure
            Log::error('Batch failed', ['exception' => $e]);
        })->finally(function (Batch $batch) {
            // Batch completed
            Cache::tags(['user-processing'])->forget("user-{$this->user->id}");
        })->dispatch();
    }
}
```

## 74. How would you implement a feature flag system?

Feature flag implementation:
```php
class FeatureFlag
{
    public static function isEnabled(string $feature): bool
    {
        return Cache::remember("feature-{$feature}", now()->addMinutes(5), function () use ($feature) {
            return DB::table('feature_flags')
                ->where('name', $feature)
                ->where('enabled', true)
                ->where(function ($query) {
                    $query->whereNull('expires_at')
                          ->orWhere('expires_at', '>', now());
                })->exists();
        });
    }
}

// Usage in blade
@feature('new-ui')
    <x-new-interface />
@else
    <x-legacy-interface />
@endfeature
```

## 75. How would you implement a robust search system with multiple indexes?

Advanced search implementation:
```php
class SearchService
{
    public function search(string $query, array $filters = [])
    {
        return Product::search($query)
            ->within('products_' . app()->getLocale())
            ->whereIn('category_id', $filters['categories'] ?? [])
            ->when($filters['price_range'] ?? false, function ($search, $range) {
                return $search->whereBetween('price', [$range['min'], $range['max']]);
            })
            ->orderBy($filters['sort'] ?? '_score', $filters['direction'] ?? 'desc')
            ->take($filters['per_page'] ?? 25)
            ->get();
    }
}
```

These questions cover advanced topics that senior Laravel developers should be familiar with. Each demonstrates practical implementation details and best practices for complex scenarios.

**Reference:** [Laravel Advanced Documentation](https://laravel.com/docs)

