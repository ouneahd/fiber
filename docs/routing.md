# Routing


## Paths
Route paths, in combination with a request method, define the endpoints at which requests can be made. Route paths can be strings, string patterns, or regular expressions.

The characters ?, +, "8", and () are subsets of their regular expression counterparts. The hyphen (-) and the dot (.) are interpreted literally by string-based paths.

Here are some examples of route paths based on strings.

```go
// This route path will match requests to the root route, /.
app.Get("/", func(c *fiber.Ctx) {
  c.Send("root")
})

// This route path will match requests to /about.
app.Get("/about", func(c *fiber.Ctx) {
  c.Send("about")
})

// This route path will match requests to /random.text.
app.Get("/random.text", func(c *fiber.Ctx) {
  c.Send("random.text")
})
```
Here are some examples of route paths based on string patterns.
```go
// This route path will match acd and abcd.
app.Get("/ab?cd", func(c *fiber.Ctx) {
  c.Send("/ab?cd")
})

// This route path will match abcd, abbcd, abbbcd, and so on.
app.Get("/ab+cd", func(c *fiber.Ctx) {
  c.Send("ab+cd")
})

// This route path will match abcd, abxcd, abRANDOMcd, ab123cd, and so on.
app.Get("/ab*cd", func(c *fiber.Ctx) {
  c.Send("ab*cd")
})

// This route path will match /abe and /abcde.
app.Get("/ab(cd)?e", func(c *fiber.Ctx) {
  c.Send("ab(cd)?e")
})
```

## Parameters
Route parameters are named URL segments that are used to capture the values specified at their position in the URL. The captured values can be retrieved using the [Params](context#params) function, with the name of the route parameter specified in the path as their respective keys.

To define routes with route parameters, simply specify the route parameters in the path of the route as shown below.

```go
app.Get("/user/:name/books/:title", func(c *fiber.Ctx) {
	c.Write(c.Params("name"))
	c.Write(c.Params("title"))
})

app.Get("/user/*", func(c *fiber.Ctx) {
	c.Send(c.Params("*"))
})

app.Get("/user/:name?", func(c *fiber.Ctx) {
	c.Send(c.Params("name"))
})
```
?>The name of route parameters must be made up of “word characters” ([A-Za-z0-9_]).

!> The hyphen (-) and the dot (.) are not interpreted literally yet, planned for V2

## Middleware
The [Next](context#next) function is a function in the [Fiber](https://github.com/fenny/fiber) router which, when called, executes the next function that matches the current route.

Functions that are designed to make changes to the request or response are called middleware functions.

Here is a simple example of a middleware function that prints "ip > path" when a request to the app passes through it.

```go
app := fiber.New()
app.Get(func(c *fiber.Ctx) {
	fmt.Println(c.IP(), "=>", c.Path())
	// => Prints ip and path

	c.Set("X-Logged", "true")
	// => Sets response header

	c.Next()
	// Go to next middleware
})
app.Get("/", func(c *fiber.Ctx) {
	c.Send("Hello, World!")
})
app.Listen(8080)
```

*Caught a mistake? [Edit this page on GitHub!](https://github.com/Fenny/fiber/blob/master/docs/routing.md)*