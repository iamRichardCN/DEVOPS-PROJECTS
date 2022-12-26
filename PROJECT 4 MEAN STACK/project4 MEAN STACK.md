
# MEAN STACK DEPLOYMENT TO UBUNTU IN AWS

This is the fourth project of this series and it comes after deploying the LAMP, LEMP and MERN Web stacks. For this project, i will be deploying MEAN stack to Ubuntu server. MEAN Stack is a combination of following components:

MongoDB (Document database) – Stores and allows to retrieve data.

Express (Back-end application framework) – Makes requests to Database for Reads and Writes.

Angular (Front-end application framework) – Handles Client and Server Requests

Node.js (JavaScript runtime environment) – Accepts requests and displays results to end user


In this project i am going to implement a simple Book Register web form using MEAN stack.The process is generally broken down into five major steps, they are;

Step 1: Install NodeJs

Step 2: Install MongoDB

Step 3: Install Express and set up routes to the server

Step 4: Access the routes with AngularJS

step 5: Starting the server


## Step 1: Install NodeJs
Node.js is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js is used in this project to set up the Express routes and AngularJS controllers.

Update ubuntu

``` sudo apt update ```

Upgrade ubuntu


``` sudo apt upgrade ```

Add certificates


``` 
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash - 
```
Install NodeJS


```sudo apt install -y nodejs ```

## Step 2: Install MongoDB

MongoDB stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.
mages/WebConsole.gif

**Install MongoDB**

<img width="953" alt="mongodb key server and installation" src="https://user-images.githubusercontent.com/111741533/202573587-2f9cfc2f-cad8-42e2-bcc9-1ffead511ebb.png">


**Start The server and Install npm – Node package manager**

<img width="372" alt="start mongo db and install -y npm" src="https://user-images.githubusercontent.com/111741533/202574157-acae9aaa-9019-4109-93b4-00fe24a3db05.png">

**Install body-parser package** (we need ‘body-parser’ package to help us process JSON files passed in requests to the server)

<img width="557" alt="installing body parser" src="https://user-images.githubusercontent.com/111741533/202574436-fd8bf487-4555-476a-87c6-f43316eed72e.png">


after Installing the body-parser, i created a **Create a folder named ‘Books’** and in the Books directory i **Initialize npm project**

<img width="422" alt="making the books dir and npm init" src="https://user-images.githubusercontent.com/111741533/202574863-5a586dba-27a6-43b2-8d2a-ee06c3accd73.png">

the server.js was then added to the books folder with the touch command after which the code in the server.js was editted to added to the following code 

```
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});
```
<img width="520" alt="creating and editing server js in books" src="https://user-images.githubusercontent.com/111741533/202575742-b4ae53b0-ed8c-40cd-8bfc-e3946f79b76a.png">


## Step 3: Install Express and set up routes to the server
Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database.

i used the Mongoose to establish a schema for the database to store data of our book register.

```sudo npm install express mongoos```

In **‘Books’** folder, i created a folder named **apps** and in the apps folder Create a file named **routes.js**

<img width="343" alt="making apps and the routes js edit" src="https://user-images.githubusercontent.com/111741533/202576893-727d2084-8537-4b7d-a434-c18805c65232.png">

as seen in the image above, the following code snippet was added to the routes.js file

```
var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};
```

in the **‘apps’** folder i created a folder named **models** , in the models folder i created a file named **book.js**

<img width="385" alt="making models and book js file" src="https://user-images.githubusercontent.com/111741533/202577256-cc5510c5-dc5d-4d1a-be9b-1a951dc41eaa.png">

editting the **books.js** file

<img width="358" alt="edtting books js in models" src="https://user-images.githubusercontent.com/111741533/202577602-585ae22a-6dcc-4c3d-9b26-e6a627d5ed01.png">


## Step 4 – Access the routes with AngularJS

AngularJS provides a web framework for creating dynamic views in your web applications. In this tutorial, we use AngularJS to connect our web page with Express and perform actions on our book register.

in the **books folder** i created another folder named **public.** In the public folder i made the **script.js** file and editted to ensure the controller configuration is defined 

<img width="424" alt="editing public script js" src="https://user-images.githubusercontent.com/111741533/202578024-fbd28f24-cd7d-448d-a33f-210c9ec7f79d.png">

```
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```

still in the **public folder**, I then created a file named **index.html**

<img width="584" alt="creating and editing the public index html" src="https://user-images.githubusercontent.com/111741533/202578605-315eccc8-b5da-4908-8d84-302e0274ede9.png">

indext.html code;

```
<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>
```

## step 5: Starting the server

I Navigated back to the books directory then i started the server by running this command: 

```node server.js```

<img width="514" alt="running the node server js" src="https://user-images.githubusercontent.com/111741533/202578978-52252fa2-b21a-48bc-a672-a2aae62d3320.png">

seeing that the app is running on port 3300, there was a need to edit the inbound rules on the Ec2 instance to accommodate this change 

<img width="584" alt="image" src="https://user-images.githubusercontent.com/111741533/202579282-7636e4a9-c342-434d-b385-7c5d4a842b12.png">

Now i could  access our Book Register web application from the Internet with a browser using Public IP address or Public DNS name


<img width="808" alt="final output on terminal" src="https://user-images.githubusercontent.com/111741533/202579383-5e16c7ae-cea9-437a-8ab4-4ee67507137e.png">




<img width="959" alt="final web output" src="https://user-images.githubusercontent.com/111741533/202579395-cf01788d-82ef-4c60-b67d-c73ff48ddaf6.png">
