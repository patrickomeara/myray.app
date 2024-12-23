---
title: Ray Methods in Laravel
menuTitle: Usage
weight: 3
---

Inside a Laravel application, you can use all methods from [the framework agnostic version](/docs/php/vanilla-php/usage).

Additionally, you can use these Laravel specific methods. Sometimes you may want to log something to Ray and get the resulting return value of your closure instead of an instance of `Ray`. You can achieve this by adding a return value type to your closure. See the examples for `showQueries()` and `countQueries()` below. Any other methods that accept a closure will function the same way.

## Showing events

You can display all events that are executed by calling `showEvents` (or `events`).

```php
ray()->showEvents();

event(new TestEvent());

event(new TestEventWithParameter('my argument'));
```

![screenshot](/screenshots/events.png)

To stop showing events, call `stopShowingEvents`.

```php
ray()->showEvents();

event(new MyEvent()); // this event will be displayed

ray()->stopShowingEvents();

event(new MyOtherEvent()); // this event won't be displayed.
```

Alternatively, you can pass a callable to `showEvents`. Only the events fired inside that callable will be displayed in Ray.

```php
event(new MyEvent()); // this event won't be displayed.

ray()->showEvents(function() {
    event(new MyEvent()); // this event will be displayed.
});

event(new MyEvent()); // this event won't be displayed.
```

## Showing jobs

You can display all jobs that are executed by calling `showJobs` (or `jobs`).

```php
ray()->showJobs();

dispatch(new TestJob('my-test-job'));

```

![screenshot](/screenshots/jobs.png)

To stop showing jobs, call `stopShowingJobs`.

```php
ray()->showJobs();

dispatch(new TestJob()); // this job will be displayed

ray()->stopShowingJobs();

dispatch(new MyTestOtherJob()); // this job won't be displayed.
```

Alternatively, you can pass a callable to `showJobs`. Only the jobs dispatch inside that callable will be displayed in Ray.

```php
event(new TestJob()); // this job won't be displayed.

ray()->showJobs(function() {
    dispatch(new TestJob()); // this job will be displayed.
});

event(new TestJob()); // this job won't be displayed.
```

## Showing cache events

You can display all cache events using `showCache`

```php
ray()->showCache();

Cache::put('my-key', ['a' => 1]);

Cache::get('my-key');

Cache::get('another-key');
```

![screenshot](/screenshots/cache.png)

To stop showing cache events, call `stopShowingCache`.

## Showing context

Laravel 11 introduced [the ability to set context](https://laravel.com/docs/11.x/context).

You can display all context using Ray's `context` method.

```php
ray()->context(); // displays all context

ray()->context('key', 'key2'); // displays only the given keys
```

![screenshot](/screenshots/context.png)

Context can also be invisible. You can display those values using the `hiddenContext` method.

```php
ray()->hiddenContext(); // displays all hidden context

ray()->hiddenContext('key', 'key2'); // displays only the given hidden keys
```

![screenshot](/screenshots/context-hidden.png)

## Handling models

Using the `model` function, you can display the attributes and relations of a model.

```php
ray()->model($user);
```

![screenshot](/screenshots/model.png)

The `model` function can also accept multiple models and even collections.

```php
// all of these models will be displayed in Ray
ray()->model($user, $anotherUser, $yetAnotherUser);

// all models in the collection will be display
ray()->model(User::all());

// all models in all collections will be displayed
ray()->model(User::all(), OtherModel::all());
```

Alternatively, you can use `models()` which is an alias for `model()`.

## Displaying mailables

Mails that are sent to the log mailer are automatically shown in Ray, you can also display the rendered version of a specific mailable in Ray by passing a mailable to the `mailable` function.

```php
ray()->mailable(new TestMailable());
```

![screenshot](/screenshots/mailable.png)

## Showing which views are rendered

You can display all views that are rendered by calling `showViews`.

```php
ray()->showViews();

// typically you'll do this in a controller
view('welcome', ['name' => 'John Doe'])->render();
```

![screenshot](/screenshots/views.png)

To stop showing views, call `stopShowingViews`.

## Displaying markdown

View the rendered version of a markdown string in Ray by calling the `markdown` function.

```php
ray()->markdown('# Hello World');
```

![screenshot](/screenshots/markdown.png)

## Displaying collections

Ray will automatically register a `ray` collection macro to easily send collections to ray.

```php
collect(['a', 'b', 'c'])
    ->ray('original collection') // displays the original collection
    ->map(fn(string $letter) => strtoupper($letter))
    ->ray('uppercased collection'); // displays the modified collection
```

![screenshot](/screenshots/collections.png)

## Usage with a Stringable

Ray will automatically register a `ray` macro to `Stringable` to easily send `Stringable`s to Ray.

```php
Str::of('Lorem')
   ->append(' Ipsum')
   ->ray()
   ->append(' Dolor Sit Amen');
```

![screenshot](/screenshots/stringable.png)


## Displaying environment variables

You can use the `env()` method to display all environment variables as loaded from your `.env` file.  You may optionally pass an array of variable names to exclusively display.

```php
ray()->env();

ray()->env(['APP_NAME', 'DB_DATABASE', 'DB_HOSTNAME', 'DB_PORT']);
```

## Using Ray with test responses

When testing responses, you can send a `TestResponse` to Ray using the `ray()` method.

`ray()` is chainable, so you can chain on any of Laravel's assertion methods.

```php
// somewhere in your app
Route::get('api/my-endpoint', function () {
    return response()->json(['a' => 1]);
});

// somewhere in a test
/** test */
public function my_endpoint_works_correctly()
{
    $this
        ->get('api/my-endpoint')
        ->ray()
        ->assertSuccessful();
}
```

![screenshot](/screenshots/response.png)

To enable this behaviour by default, you can set the `send_requests_to_ray` option in [the config file](/docs/php/laravel/configuration) to `true`.

## Showing HTTP client requests

You can display all HTTP client requests and responses using `showHttpClientRequests`

```php
ray()->showHttpClientRequests();

Http::get('https://example.com/api/users');
```

![screenshot](/screenshots/requests.png)

To stop showing HTTP client events, call `stopShowingHttpClientRequests`.

Alternatively, you can pass a callable to `showHttpClientRequests`. Only the HTTP requests made inside that callable will be displayed in Ray.

```php
Http::get('https://example.com'); // this request won't be displayed.

ray()->showHttpClientRequests(function() {
    Http::get('https://example.com'); // this request will be displayed.
});

Http::get('https://example.com'); // this request won't be displayed.
```
