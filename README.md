# express-local-library
A local library website based on [MDN tutorials](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/Tutorial_local_library_website)

# learning points
The learning points are my own and not comprehensive:\
node.js, express, mongodb, express-generator, mvc, schema, callbacks, express-validator.

## node.js
Node.js is the python interpreter for javascript, and it has its own pip-like package manager, npm. With node.js, you can write JS code on the server side instead of just using it for client side logic. You can start a project with `npm init`, it will create a package.json file within the directory, which contains the project name, scripts and dependencies. You will usually have some start scripts such as `npm start` which will start the application, but you can also specify other start scripts with different environment variables. To install new modules, you just run `npm install module` or `npm install -g module` to install globally.

## express
Express is a web development framework for node.js, much like Django. 

## mongodb
MongoDB is a non-relational DB that means it doesn't store data as tables like SQL database. It is a service, which means you have to download it, start it up and connect to it externally. The module that talks to MongodDB in node is called mongoose.

## express-generator
It creates default files for your web app when you call `express` such as templates, routers and views, much like `django-admin startproject mysite` command in Django. It also creates a node_module in your folder which is like a virtual env for your node modules.

## mvc
Mvc stands for model-view-controller, a standard architecture to create applications and supported natively by express. Basically users see a view and uses a controller to submit an action. The action interacts with the model which is a database object wrapped in OOP code (it supports CRUD - create, read, update, delete directly on the db). The points of interactions are called endpoints which are represented by URL. The mapper between endpoints and controllers are called routers.

## schema
Schema basically means the OOP modelling of the database object. For example, what is its name, id and attributes. Usually only relational database has schema as they need to be well defined in order to be stored. Indeed, when you have slight change to the schema you would need to call a migrate on the db to move the data to the new schema but not for non-relational dbs as they are supposed to be able to store untidy data. Why does MongoDB has a schema? This [article](http://billpatrianakos.me/blog/2016/07/07/mongodb-and-mongoose-why-define-a-schema/) tells us that basically its for ease of usage in our web application

## callbacks
Now this is a pretty funky new topic for me. I was stunned by the async module used in the code
    
    var async = require('async');
    
    async.parallel({
      genre: function(callback) {
        Genre.findById(req.params.id)
          .exec(callback);
      },

      genre_books: function(callback) {
        Book.find({ 'genre': req.params.id })
          .exec(callback);
      },

      }, function(err, results) {
        if (err) { return next(err); }
        if (results.genre==null) { // No results.
          var err = new Error('Genre not found');
          err.status = 404;
          return next(err);
        }
        // Successful, so render
        res.render('genre_detail', { title: 'Genre Detail', genre: results.genre, genre_books: results.genre_books});
      })
      
I couldn't fathom how did the first function in async.parallel would know what a callback is? its not even defined anywhere. Actually this is just how async is implemented. Under the hood, it would assign the callback function to the various functions above it. A python code that could show how this works is as below:

    def async_parallel(list, callback):
        for func in list:
            func(callback)
        return

    def a(*args):
        # doing something
        string = ("Callback is real")
        # calls the callback with its results
        args[0](string)


    def b(*args):
        string = ("Hello World")
        args[0](string)


    def c(results=None, error=None):
        if error:
            print ("I have got some errors")
        else:
            print ("I'm the callback")
            print (results)
            
    async_parallel([a, b], c)
    
Passing functions which calls other functions within functions certainly creates a lot of confusion, but this is a concept in functional programming. Callbacks are really important in js as many functions in JS runs asynchronously (You write some program to fetch data from Google, you don't know when it will return) . Callbacks are basically checkpoints where you can gather the results of the various functions that has been running asynchronously. A typical callback has the following form:

    callback: function (err, results) {
        if (err) {
            // do something;
        } else {
            // process result;
        }
    }
    
Functions in modules that has the callback argument expects the callback in this form and will call them when they return (hence the name "Call-back"). A bad programming practice is [callback hell](http://callbackhell.com/), where you try to force programs to be synchronous by daisy chaining them together. A better way to do it is to analyze the various functions, determine which one can be callback together and tie them up manually or with some library like async. There also exist other solutions to this asynchronous problem (e.g. promise framework).

## express-validator
Validation of data is a pretty important job as people might send rubbish data through forms and APIs. While you can control the data through form with some frontend validators, you usually can't control the API endpoints so validation also needs to be done in the backend. Express-validator is a library that allows you to validate and sanitize the data coming in. Sanitization basically means making sure the data doesn't contain malicious code such as those that can lead to [SQL injection](https://www.youtube.com/watch?v=_jKylhJtPmI).

# future features:
I think node is a pretty cool framework and this setup is pretty much the MEAN stack (mongoDB, express, angular, node.js), which I think is pretty popular. Besides, I happen to keep a read list in an excel file and I hope to port my file to the web application:

1. Have a dashboard that shows reading stats
2. Maybe implement some web scrapping to see which good books I like
3. Download/upload csv functions
4. Making it look nice with some frontend framework
