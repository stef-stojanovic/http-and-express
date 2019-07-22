# HTTP and Express

## Outline
* Create a basic HTTP server with Express
* Restart a JavaScript program with `nodemon`
* Use query params to create dynamic routes
* Use middleware to parse body data from a request

## Lesson

### Create a basic HTTP server with Express

Now that we've learned how to run JavaScript from the terminal with Node.js, let's set up a basic server for http requests.

As with most backend languages, setting a server up from scratch would take forever, so we'll skip ahead a little bit using an npm package named `express`.

Let's review how the internet works:

**Clients and Servers communicate through Requests and Responses**

```
					-> Request ->
Client(frontend)						Server (backend)
					<- Response <-
```

Browsers send requests to a server. The user either does this indirectly, through their URL bar, or indirectly, via a `fetch` request sent by our frontend code.

**Requests have several parts:**

* The HTTP method (GET, POST, PATCH, etc.)
* The path (`/users`, etc.)
* Headers (like `Content-Type` or `Accept`)
* A body (usually where we put the bulk of the data we need to send) 

With express, we will define "handlers", configured to respond specific http requests.

Let's start by installing `express`:



> Terminal

```
npm install express
```



Then open up `index.js` and `require` express into your app:



> `index.js`

```javascript
const express = require('express')
const app = express()
```



`express`- as it's required here, is a function. On the second line, we're invoking that function to get an `app` object. The `app` object express created for us will represent our backend application. 

It has a `get` method, which accepts two arguments:

1. A string, representing a path we would like to listen for requests on
2. A callback function, to be invoked whenever a request is made to the provided path

Let's create a simple request handler, then tell our app what port to listen on



> `index.js`

```javascript
const express = require('express')
const app = express()

app.get('/hello-world', () => {
    console.log('A GET request was made to "/hello-world"!')
})

app.listen(8080)
```



Use the `node` terminal command to  run `index.js` If it looks like the terminal got stuck, that's a good sign. :)

Open up your browser and go to http://localhost:8080/hello-world. Then look back in your terminal. You should see `A GET request was made to "/hello-world"!` in the terminal output. Your `console.log` ran when you made the request from your browser!

However, your browser is probably still spinning away. That's because we haven't told express to `respond` to our the request yet. 

The callback we passed in to `app.get` is going to recieve two arguments provided by `express` - the same way our event listeners recieve `e` as an argument in normal JavaScript:

1. An object that represents the request
2. An object that represents the response

Let's replace the `console.log` with a call to `response.send`, which will send our message back to the browser instead of logging it in the terminal. Don't forget to accept `request` and `response` as arguments for your callback!



> `index.js`

```javascript
const express = require('express')
const app = express()

app.get('/hello-world', (request, response) => {
    response.send('A GET request was made to "/hello-world"!')
})

app.listen(8080)
```



Now if you refresh the page... it's probably still spinning. That's because Node.js is still running the old version of your code- you need to shut down your server (Click the terminal and then press CTRL+C), then restart the server with `node` for your changes to take effect. This is super annoying, so let's look at a tool we can use to restart our server automatically.



### Restart a JavaScript program with `nodemon`

`nodemon` is another `npm` package (like `express`!) which can restart any Node.js app when you edit your code. First install it (the `-g` tells `npm` to install `nodemon` globally): 



> Terminal

```
npm install -g nodemon
```



Then use `nodemon` instead of `node` to run your server:



> Terminal

```
nodemon index.js
```



Now, everytime you save a file, the project should refresh automatically. At this point, you should be able to visit http://localhost:8080/hello-world and get `A GET request was made to "/hello-world"!` back. Try changing the message (maybe to `Hello World`?) then refresh your browser to see if the change is reflected immediately. 



### Use query params to create dynamic routes

Now we can respond to HTTP requests! But so far we've only seen how to respond to very simple `get` requests.  What if we want to put ids in our paths?

`express` makes this pretty easy for us. We can use a colon in the path to define a path parameter, and then access it using the `request` object we receive as an argument:



> `index.js`

```javascript
const express = require('express')
const app = express()

app.post('/hello-world/:someParam', (request, response) => {
    response.send(`The client sent a param of: ${request.params.someParam}`)
})

app.listen(8080)
```



### Use middleware to parse body data from a request

What if we want to use a different HTTP method? Like POST? or DELETE?

`express` makes this pretty easy for us. We can create a handler for another HTTP method simply by using different methods on our `app` object: 



> `index.js`

```javascript
const express = require('express')
const app = express()

app.post('/hello-world', (request, response) => {
    response.send('A POST request was made to "/hello-world"!')
})

app.listen(8080)
```



Now our handler won't be called unless a POST request is made to that path. Use Postman to send a POST request to http://localhost:8080/hello-world, and see if you get a response back.

But how do we get data from the body of a request?

We have to go through one more step before we can access body data. And that's because data in the body of a request is encoded- usually as a JSON string. We need to tell `express` to parse the body of the request *before* we try to handle the request and send a response.

First, we'll require yet *another* `npm` package (this one is already installed), called `bodyParser`. Then, we'll tell our `app` to `use` the `bodyParser` to parse `json` data:



> `index.js`

```javascript
const express = require('express')
const bodyParser = require('body-parser')
const app = express()

app.use(bodyParser.json())

app.post('/hello-world', (request, response) => {
    response.send('A POST request was made to "/hello-world"!')
})

app.listen(8080)
```



Finally, we should be able to access the body data using `request.body`: 



> `index.js`

```javascript
const express = require('express')
const bodyParser = require('body-parser')
const app = express()

app.use(bodyParser.json())

app.post('/hello-world', (request, response) => {
    console.log(request.body)
    response.send('A POST request was made to "/hello-world"!')
})

app.listen(8080)
```



Use Postman to make a POST request to http://localhost:8080/hello-world, send some JSON data in the body, and see if it shows up in your terminal!



## Check Your Understanding

Now that we can respond to HTTP requests, use path params and JSON body data, we should be able to implement full, RESTful CRUD operations on a JavaScript array. 

1. Create an empty array named `fruits` in `index.js`.
2. `fruits` should be an array of objects, with object having a `name` and `color` property.
3. Implement HTTP methods to Create, Read, Update, and DELETE fruit from the array