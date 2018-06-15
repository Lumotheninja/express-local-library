# express-local-library
A local library website based on [MDN tutorials](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/Tutorial_local_library_website)

# learning points
node.js, express, mongodb, express-generator, mvc, schema, callbacks, validation.

## node.js
Node.js is the python interpreter for javascript, and it has its own pip-like package manager, npm. With node.js, you can write JS code on the server side instead of just using it for client side logic. You can start a project with `npm init`, it will create a package.json file within the directory, which contains the project name, scripts and dependencies. You will usually have some start scripts such as `npm start` which will start the application, but you can also specify other start scripts with different environment variables. To install new modules, you just run `npm install` or `npm install -g` to install globally.

## express
Express is a web development framework for node.js, much like Django. 

## mongodb
MongoDB is a non-relational DB that means it doesn't store data as tables like SQL database. It is a service, which means you have to download it, start it up and connect to it externally. The module that talks to MongodDB in node is called mongoose.

## express-generator
It creates default files for your web app when you call `express` such as templates, routers and views, much like `django-admin startproject mysite` command in Django. It also creates a node_module in your folder which is like a virtual env for your node modules.

## mvc
Mvc stands for model-view-controller, a standard architecture to create applications and supported natively by express. Basically users see a view and uses a controller to submit an action. The action interacts with the model which is a database object wrapped in OOP code (CRUD - create, read, update, delete). The points of interactions are called endpoints which are represented by URL. The mapper between endpoints and controllers are called routers.

## schema
Schema basically means the OOP modelling of the database object. For example, what is its name, id and attributes. Usually only relational database has schema as they need to be well defined in order to be stored. Indeed, when you have slight change to the schema you would need to call a migrate on the db to move the data to the new schema. Why does MongoDB has a schema? This [article](http://billpatrianakos.me/blog/2016/07/07/mongodb-and-mongoose-why-define-a-schema/) tells us that basically its for ease of usage in our web application

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
    
Passing functions which calls other functions within functions certainly creates a lot of confusion, but this is a concept in functional programming. Callbacks are really important in js as many functions in js runs asynchronously (You try to fetch data from Google, you don't know when you will be back) . Callbacks are basically checkpoints where you can gather the results of the various functions that has been running asynchronously. A typical callback has the following form:

    callback: function (err, results) {
        if (err) {
            // do something;
        } else {
            // process result;
        }
    }
    
Functions in modules that has the callback argument expects the callback in this form 
