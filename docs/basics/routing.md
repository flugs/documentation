# Flugs Router

## Basic Routing

```c++
int main(int argc, char **argv)
{
    QCoreApplication app(argc, argv);

    HttpServer server;
    Router router(&server);

    router.get("/", [](Request req, Response *res) {
        res->write("Hello World!\n");
        res->end();
    });

    router.post("/foo", [](Request req, Response *res) {
        res->write("Hello World!\n");
        res->end();
    });

    router.put("/foo", [](Request req, Response *res) {
        //...
    });

    router.del("/foo", [](Request req, Response *res) {
        //...
    });


    if(!server.listen(QHostAddress::Any, 8080)) {
        exit(1);
    }

    app.exec();
}
```

## Route Parameters

### Required Parameters

Of course, sometimes you will need to capture segments of the URI within your
route. For example, you may need to capture a user's ID from the URL.
You may do so by defining route parameters:

```c++
router.get("/user/:id", [](Request req, Response *res) {
    QString id = req->param("id");
    res->write(id);
    res->end();
});
```

You may define as many route parameters as required by your route:

```c++
router.get("/posts/:post/comments/:comment", [](Request req, Response *res) {
    //...
});
```

> **Note:** Route parameters cannot contain the `-` character. Use an underscore (`_`) instead.

### Regular Expression Constraints

You may constrain the format of your route parameters by defining a regular
expression in your route definition:

```c++
router.get("/user/{name:[A-Za-z]+}", [](Request req, Response *res) {
    //...
});
```

## Named Routes

Named routes allow you to conveniently generate URLs or redirects for a
specific route. You may specify a name for a route using the `name` method
when defining the route:

```c++
router.get("/", [](Request req, Response *res) {
    //...
}).name("root");
```

### Generating URLs To Named Routes

Once you have assigned a name to a given route, you may use the route's name
when generating URLs:

```c++
QUrl url = Route::to("root");
```

Or to redirect:
```c++
redirect()->to("root");
```

If the route defines parameters, you may pass the parameters as arguments
to the `to` method. The given parameters will automatically be inserted
into the URL:

```c++
router.get("/posts/:post/comments/:comment", [](Request req, Response *res) {
    //...
}).name("post_comment");

QUrl url = Route::to("post_comment", 1, 3);
```


## Route Scopes

Route scopes allow you to share route attributes, such as middleware or
path prefixes, across a large number of routes without needing to define those
attributes on each individual route.

To learn more about route scopes, we'll walk through several common use-cases
for this feature.

### Middleware

To assign middleware to all routes within a scope, you may use the `use` method.
Middleware will be executed in the order they are define:

```
TODO
```

### Path Prefix

The `pathPrefix` method may be used to prefix each route in the group with a
given URI. For example, you may want to prefix all route URIs within the
scope with `admin`:
```c++
Scope &s = router.subScope().pathPrefix("admin");
s.get("/", [](Request req, Response *res) {
    // Matches the "/admin" URL
});

s.get("/users", [](Request req, Response *res) {
    // Matches the "/admin/users" URL
});
```

You may also use the `pathPrefix` parameter to specify common parameters
for your scoped routes:
```c++
Scope &s = router.subScope().pathPrefix("account/:id");
s.get("/", [](Request req, Response *res) {
    // Matches the "/account/:id" URL
});

s.get("/detail", [](Request req, Response *res) {
    // Matches the "/account/:id/detail" URL
});
```
