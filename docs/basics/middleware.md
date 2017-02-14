# Flugs Middleware

- [Introduction](#introduction)
- [Defining Middleware](#defining-middleware)
- [Registering Middleware](#registering-middleware)
- [Middleware Parameters](#middleware-parameters)
- [Terminable Middleware](#terminable-middleware)

<a name="introduction"></a>
## Introduction

Flugs middleware provide a convenient mechanism for filtering HTTP requests
and to postprocessing HTTP responses.

For example, Flugs includes a middleware that verifies the user of your
application is authenticated. If the user is not authenticated, the middleware
will redirect the user to the login screen.

However, if the user is authenticated, the middleware will allow the request
to proceed further into the application.


## Using Middleware

## Defining Middleware
```c++
class ExampleMiddleware : public Middleware
{
public:
    void handle(Request request, Response *response) override
    {
        next(request, response);
    }
};
```

It's best to envision middleware as a series of "layers" HTTP requests must
pass through before they hit your application. Each layer can examine the
request and even reject it entirely.


### *Before* / *After* Middleware

Whether a middleware runs before or after a request depends on the middleware
itself. For example, the following middleware would perform some task
**before** the request is handled by the application:

```c++
class BeforeMiddleware : public Middleware
{
public:
    void handle(Request request, Response *response) override
    {
        // Perform something
        std::cout << "Hello World!" << std::endl;

        next(request, response);
    }
};
```

However, this middleware would perform its task **after** the request is
handled by the application:

```c++
class AfterMiddleware : public Middleware
{
public:
    void handle(Request request, Response *response) override
    {        
        next(request, response);

        // Perform something
        std::cout << "Goodbye!" << std::endl;
    }
};
```

## Registering Middleware

A middleware can be assigned to a `Router`, to a `Scope` or to a specific `Route`.

If you want a middleware to be run during every HTTP request, simply add the
middleware to your top level Scope - your Router - using the `use` mehtod.

```c++
Router r;
r.use(new MyFirstMiddleware());
```

If you would like to assign the middleware to a specific Scope, you can also use
the `use` method to add the middleware.

```c++
Scope &s = r.subScope();
s.use(new MySecondMiddleware());
```

It is also posible to add a middleware just to a specific route.

```c++
r.get("/", [](Request req, Response *res) {
    //...
}).use(new MyThirdMiddleware());
```

!!! note
    A middleware registered in the parent scope is also invoked for
    all children routes and scopes.


<a name="terminable-middleware"></a>
## Terminable Middleware

Sometimes a middleware may need to do some work after the HTTP response has
already been sent to the client. To accomplish this, define the middleware as
"terminable" by implementing the `terminate` method:

```
class MyMiddleware : public Middleware
{
public:
    void terminate(const Request &req, const Response *res) override
    {
    }
};
```
