openshift-nodejs-mongo-mysql
============================

Connect to OpenShift with Node.js, MongoDB and MySQL

```

var express = require('express');
var monk = require('monk');
var mysql = require('mysql');

var app = express();

//app configuration
var ipaddr = process.env.OPENSHIFT_NODEJS_IP || '127.0.0.1';
var port = process.env.OPENSHIFT_NODEJS_PORT || 8080;

//mongodb configuration
var mongoHost = process.env.OPENSHIFT_MONGODB_DB_HOST || 'localhost';
var mongoPort = process.env.OPENSHIFT_MONGODB_DB_PORT || 27017;
var mongoUser = ''; //mongodb username
var mongoPass = ''; //mongodb password
var mongoDb   = ''; //mongodb database name

//mysql configuration
var mysqlHost = process.env.OPENSHIFT_MYSQL_DB_HOST || 'localhost';
var mysqlPort = process.env.OPENSHIFT_MYSQL_DB_PORT || 3306;
var mysqlUser = ''; //mysql username
var mysqlPass = ''; //mysql password
var mysqlDb   = ''; //mysql database name

//connection strings
var mongoString = 'mongodb://' + mongoUser + ':' + mongoPass + '@' + mongoHost + ':' + mongoPort + '/' + mongoDb;
var mysqlString = 'mysql://'   + mysqlUser + ':' + mysqlPass + '@' + mysqlHost + ':' + mysqlPort + '/' + mysqlDb;

//connect to mongo
var mongoClient = monk(mongoString, function(err){
  if (err) console.log(err);
});

//connect to mysql
var mysqlClient = mysql.createConnection(mysqlString);
mysqlClient.connect(function(err){
  if (err) console.log(err);
});


// app is running!
app.get('/', function(req, res) {
  res.send('OK');
});


// MongoDB is running!
app.get('/mongo', function(req, res) {
  var users = users = mongoClient.get('users');
  users.find({ name: 'Loki' }, function(err, data){
    if (err) {
      res.send('NOT OK' + JSON.stringify(err));
    } else {
      res.send('OK');
    }
  });
});

//MySQL is running!
app.get('/mysql', function(req, res) {
  mysqlClient.query('SELECT 1 + 1 AS solution', function(err, rows, fields) {
    if (err) {
      res.send('NOT OK' + JSON.stringify(err));
    } else {
      res.send('OK: ' + rows[0].solution);
    }
  });
});


app.listen(port, ipaddr);

console.log('Server running at http://' + ipaddr + ':' + port + '/');
console.log('MongoDB running at mongodb://[user:password]@' + mongoHost + ':' + mongoPort + '/nodejs');
console.log('MySQL running at mysql://[user:password]@' + mysqlHost + ':' + mysqlPort + '/nodejs');

```
