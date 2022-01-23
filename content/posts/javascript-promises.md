---
title: "Javascript Promises"
date: 2015-09-20T15:02:18-08:00
draft: false
---

JavaScript is a non-blocking programming language, which means that one chunk of JS code will not necessarily wait for the lines above it to execute before beginning itself. In many cases this is a pretty awesome feature of the language. Imagine if your website was only able to run one window.onload call at a time. If your page needed to perform a number of calculations or data insertions in order to render correctly, things would take forever to load and users would see a bunch of unstyled content and blank boxes at the beginning of every page load. Yuck.

While non-blocking is a very desirable feature of JavaScript in the browser, the paradigm shifts when writing server code with Node.js. Servers often need to wait for a data response so that they can manipulate and serve that data correctly. This requirement most commonly arises during data read and write operations. In these cases, code executing out of order can cause serious problems with application logic.

The most commonly used solution to this problem in the JavaScript community has been callbacks. A callback is a piece of executable code that is passed as an argument to other code, which is expected to call back (execute) the argument at some convenient time. Here’s an example of a callback that almost all  Node.js developers have seen:

```
mongodb.connect('mongodb://localhost:27017/mydb', function(err, db) {
    if (err) {
        throw err;
    } else {
        console.log('Successfully connected to the database');
    }
    var contentCollection = db.collection('content');
    contentCollection.find({}).toArray(function(err, result) {
        if (err) {
            throw err;
        } else {
            res.status(200).json(result);
            db.close();
        }
    });
});
```


In this example, we’re using the MongoDB native driver to connect to the database, grab all the data from a collection called “content” and return that data as JSON. Due to JavaScript’s non-blocking nature, however, we can’t make the `.find({})` call until we’re sure we have connected successfully to the database, so we pass a callback into `mongodb.connect();`. Similarly, we cannot reliably return the data via `res.json()` until we are sure the `.find({})` operation has completed successfully, so we pass a second callback into `.find({}).toArray()`.

---

#### Why Do Callbacks Suck?

This doesn’t look so bad, but what happens if you need to pass a third, or fourth or fifth callback in order to manipulate the data further? A relatively simple algorithm can quickly turn into something Node.js developers term “callback hell”.

![Callback hell](/images/javascript-promises/gn3N5gB.png)

Code like this can be very hard to understand at first glance, and looks extremely unwieldy in a text editor. Callbacks are essentially an inelegant hack for getting around JavaScript’s non-blocking nature. There has to be a better way…

---

#### Enter JavaScript Promises

Promises are a better way to write asynchronous JavaScript. A promise is essentially an object that, when returned from a function, represents an operation that hasn’t completed yet but is expected to in the future. Because that definition didn’t make sense to me the first time I read it either, here’s an example of the above MongoDB code written using promises instead of callbacks:

```
mongoClient.connectAsync('mongodb://localhost:27017/mydb')
    .then(function(db) {
        return db.collection('content').findAsync({})
    })
    .then(function(cursor) {
        return cursor.toArrayAsync();
    })
    .then(function(content) {
        res.status(200).json(content);
    })
    .catch(function(err) {
        throw err;
    });
```

Pretty cool, right? This code is cleaner-looking and way easier to understand than its callback-infested cousin. “But”, you may be asking, “where did you get connectAsync() from? When I run that with Node I just get an error!”

---

#### Using the Bluebird Promise Library with Node.js

[Bluebird](https://www.npmjs.com/package/bluebird) is a fully-featured promise library for JavaScript. The project’s NPM package means that integration with a Node.js app is fairly simple. Bluebird’s strongest feature is that it allows you to “promisify” other Node modules in order to use them asynchronously. After running `npm install --save bluebird` on your Node project, you can use the following code to “promisify” the MongoDB module and use it asynchronously:

```
/* Dependencies */
var Promise = require('bluebird');
var mongoClient = Promise.promisifyAll(require('mongodb')).MongoClient;

mongoClient.connectAsync('mongodb://localhost:27017/mydb')
    .then(function(db) {
        return db.collection('content').findAsync({})
    })
    .then(function(cursor) {
        return cursor.toArrayAsync();
    })
    .then(function(content) {
        res.status(200).json(content);
    })
    .catch(function(err) {
        throw err;
    });
```

Just use Bluebird’s `.promisifyAll()` method to create an async version of every method the module provides. It really is that easy.

---

#### Returning Promises From Other Functions

In the course of writing more sophisticated Node.js apps it sometimes becomes necessary to return a promise from your own function. Let’s extend our example above by hypothesizing that we want to be able to perform CRUD operations on the data that our DB call returns. In this particular instance, let’s implement a separate delete function that calls the code we wrote to get all content from the database, then looks through it for a specific value and deletes that value if it exists.

```
/* Dependencies */
var Promise = require('bluebird');
var mongoClient = Promise.promisifyAll(require('mongodb')).MongoClient;

/* Functions */
var getAllContent = function() {
    return mongoClient.connectAsync('mongodb://localhost:27017/mydb')
        .then(function(db) {
            return db.collection('content').findAsync({})
        })
        .then(function(cursor) {
           return cursor.toArrayAsync();
        })
        .then(function(content) {
            // This is how we return the data to the next .then() call
            return content;
        })
        .catch(function(err) {
           throw err;
        });
    });
};

var deleteItem = function(itemID) {
    getAllContent()
        .then(function(content) {
            var itemExists = false;
            for (var i = 0; i < content.length; i++) {
                if (content[i].itemID === itemID) {
                    itemExists = true;
                    break;
                }
            }
            if (itemExists) {
                // Delete the item from the DB here
            }
        })
        .catch(function(err) {
            throw err;
        });
};
```

And there you have it. Your getAllContent() function returns a promise, which you can use in your deleteItem() function.


---

#### Creating Your Own Promises

You’ll almost never need to do this, but here’s how to wrap something in your own promise.

```
/* Dependencies */
var Promise = require('bluebird');
var mongoClient = Promise.promisifyAll(require('mongodb')).MongoClient;
var unpromisifiedLib = require('upl');

/* Functions */
var readFileAsync = function() {
    return new Promise(function(resolve) {
        // Do some IO stuff which you'll almost defintely
        // be able to promisify without having to do this manually
        var contents = unpromisifiedLib.doSomething();
        resolve(contents);
    });
};

var consumeHomemadePromise = function(itemID) {
    readFileAsync
        .then(function(contents) {
            console.log(contents);
        })
        .catch(function(err) {
            throw err;
        });
};
```

Keep in mind, you’ll be able to use the Bluebird library to promisify basically anything, so its usually unnecessary to do this.

Those are the basics of how to use Bluebird promises in Node.js. For more information, check out the [Bluebird API](https://github.com/petkaantonov/bluebird/blob/master/API.md).

