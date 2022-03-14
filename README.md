# npm-express-study-guide

nodejs express webserver notes

## example 01

```bash
npm init -y
npm install express
```

then created app.js

```javascript
'use strict'

const express = require('express')

const app = express()

// This get basically means if this endpoint is requested, then run this javascript arrow function:
app.get('/',(req, res)=>{
 res.send("hello from my app")
})


app.listen(3000, ()=> {
 console.log('listening on port 3000')
})
```

Then run:

```bash
$ node app.js
listening on port 3000
```

This caused the terminal to hang since it's listening on a port.

and on another terminal run:

```bash
$ curl http://localhost:3000/ 
hello from my app
```

## example 02 - refactor

Doing `npm test` would cause app to actually start listening, and cause things to hang.

That's why split out app.js into 2 separate files. app.js and server.js. That's done for unit testing purposes only.

Now we can create appTest.js but but no need to write serverTest.js becuse there's nothing of interest that needs testing here.

Now have to start app using `node server.js`

Also updates package.json so you now can use `npm start` instead of `node server.js`

## example 03 - add linting

We're going to use the standard function to do the linting:

```bash
npm install -D standard
```

also added "lint" and "lint:fix" npm-run scripts to the package.json. So now can do:

```bash
npm run lint
npm run lint:fix
```


## example 04 - create static index.html file

Note, there's a important distinction between `app.use()` and `app.get()`:

- `app.get()` used to send responses back from a GET request
- `app.use()` used for running middleware. 


We achieve this using the `express.static()` (middleware) function - https://expressjs.com/en/starter/static-files.html

We create a new folder, called public which will hold our static files. 

We also use the `__dirname` - which is a builtin variable - https://nodejs.org/docs/latest/api/modules.html#__dirname


Now open [localhost:3000](http://localhost:3000) to view this index.html file. Or use curl:

```
curl http://localhost:3000
<head>
        <title>My Homepage</title>

</head>

<body>
        <p>My Html page</p>
</body>%                          
```

Since we are using morgan, the cli output now looks like:

```
npm start

> 01@1.0.0 start
> node server.js

Server started on port 3000
::1 - - [14/Mar/2022:13:45:37 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.51 Safari/537.36"
::1 - - [14/Mar/2022:13:45:38 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.51 Safari/537.36"
::1 - - [14/Mar/2022:13:54:33 +0000] "GET / HTTP/1.1" 200 80 "-" "curl/7.77.0"
::1 - - [14/Mar/2022:13:45:39 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.51 Safari/537.36"
```

Notice above the "morgan" output when a curl command is called too. The "morgan" line has to be put near the top, before any app.use() or app.get(), or it will not print out all the info from various requests. 

as soon as the app.use got triggered it didn't go any further to print `hello from my app`. However that's for the way the express.static() worked. In genaral if 
an callback calls  needs to run something like `res.send(content)` to signal the completion of a request. 

While the app is running, you can rename the index.html file to something else, then you'll get:

```
curl http://localhost:3000            
hello from my app
```

and if you use the new name, you get:

```
curl http://localhost:3000/index2.html
<head>
        <title>My Homepage</title>

</head>

<body>
        <p>My Html page</p>
</body>%            
```

## example 05 - Added some debugging capability. 

We introduce the `debug` method. This is a nicer alternative to using `console.log()` to print out debug messages. 

The output looks like:

```
npm run debug

> 01@1.0.0 debug
> DEBUG=* npm start


> 01@1.0.0 start
> node server.js

  express:application set "x-powered-by" to true +0ms
  express:application set "etag" to 'weak' +1ms
  express:application set "etag fn" to [Function: generateETag] +0ms
  express:application set "env" to 'development' +1ms
  express:application set "query parser" to 'extended' +0ms
  express:application set "query parser fn" to [Function: parseExtendedQueryString] +0ms
  express:application set "subdomain offset" to 2 +1ms
  express:application set "trust proxy" to false +0ms
  express:application set "trust proxy fn" to [Function: trustNone] +0ms
  express:application booting in development mode +0ms
  express:application set "view" to [Function: View] +0ms
  express:application set "views" to '/Users/sherchowdhury/github/npm-express-study-guide/examples/05/views' +0ms
  express:application set "jsonp callback name" to 'callback' +0ms
  express:router use '/' query +7ms
  express:router:layer new '/' +0ms
  express:router use '/' expressInit +0ms
  express:router:layer new '/' +0ms
  express:router use '/' logger +1ms
  express:router:layer new '/' +0ms
  express:router use '/' serveStatic +0ms
  express:router:layer new '/' +0ms
  express:router:route new '/' +0ms
  express:router:layer new '/' +0ms
  express:router:route get '/' +0ms
  express:router:layer new '/' +0ms
  myApp should now have started +0ms
Server started on port 3000
```


## example 06 - Added some debugging capability. 

Added an env var.

The from the command line run:

```
PORT=4000 npm start

> 01@1.0.0 start
> node server.js

Server started on port 4000
```

Then test it:

```
curl http://localhost:4000
<head>
        <title>My Homepage</title>

</head>

<body>
        <p>My Html page</p>
</body>%                             
```




## example 07 - Added some env vars

Just have to run like this:

```
PORT=4000 npm start

> 01@1.0.0 start
> node server.js
```


## Example 08 - replace static html pages with ejs templates


Skipped this. 

basically had to create .ejs template files. 

also have to set:

`app.set(...)`

for setting express's internal env vars - https://expressjs.com/en/api.html#app.set

Note this is also used for updating express's own config settings, e.g. the `views` setting. 


Also need to replace `res.send('xxxx')`, we use `res.render('index', key-value-object)`

this says which ejs file to use, e.g. in this case it is `index.ejs` and what data to populate it with, i.e. key-value-object. 





## example xx - add unit test

<https://github.com/Sher-Chowdhury/npm-supertest-and-nock-demo>

```bash
npm install -D mocha chai 
npm install -D supertest nock
npm install -D standard
```

Also added a few run-scripts in `package.json`.
