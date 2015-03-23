##The problem  
We have an app running on the MEAN stack and are using MongooseJS as our ORM. Now, we have 2 collections *event* and *eventV2*. We are looking to drop the *event* collection and rename the *eventV2* collection to just *event*.  

##The solution  
This means running a migration script and fortunately for us, mongoose can connect to MongoDB directly and let us use Mongo's native methods. We'll be using the `dropCollection` and `renameCollection` methods. You can read more about the two methods on their [site](http://mongodb.github.io/node-mongodb-native/api-generated/db.html#dropcollection).  

##Getting started  
We start off by requiring the required libraries. For this script, only 2 libraries are required.  
```  
var mongoose = require('mongoose');  
var async = require('async');  
```  

And then followed by initialising a variable to hold the address for our database.  
`var db = 'mongodb://localhost/myawesomeapp';`  

##Completing the script  
We will define 2 private functions - `_dropCollection` and `_renameCollection`. Their names are pretty self explanatory as to thier intended functions.  
```  
function _dropCollection(callback) {  
    mongoose.connection.db.dropCollection('event', function (err, result) {  
        if (err) { callback(err); }  
        callback(null, result);  
    });  
}
```  
The first parameter of Mongo's `dropCollection` method is the name of the collection we would like to drop. That is followed by the callback function. Simple enough, right!  

```  
function _renameCollection(callback) {  
    mongoose.connection.db.renameCollection('eventV2', 'event', function (err, result) {  
        if (err) { callback(err); }  
        callback(null, result);  
    });  
}  
```  
The `renameCollection` is pretty much the same expect that it takes 3 parameters. The first is the name of the collection we want to rename. That's followed by the new name we will be giving our collection and then lastly, the usual callback function.  

## Putting it all together  
To finish off the script, we now connect to the database and run the 2 functions we just defined.  
```  
mongoose.connect(db, function (err) {  
    if (err) { throw err; }  
    
    async.series([_dropCollection, _renameCollection], function (err, result) {  
        if (err) { throw err; }  
        console.info('Completed successfully! Result: ' + result);  
        process.exit();  
    });  
});  
```  
We use `async.series` to run each of our private functions and since order is important, `series` is the best way to go for this situation. If the migration is successfull, we exit the node process and go have a cup of coffee to celebrate.  

To run the script; in your terminal -  
`node migration.js`  

I hope this was useful into how migrations can be run against a MongoDB instance for a NodeJS application. The gist of the full script can be found [here](https://gist.github.com/riyadhalnur/64ab146dcb2098e856d4).  

This post was originally publshed on my personal blog.