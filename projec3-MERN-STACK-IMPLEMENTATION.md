# MERN STACK IMPLEMENTATION
In this project, i will be implementing a web solution based on MERN stack in AWS Cloud. Typically, a MERN Web stack consists of following components:

**MongoDB:** A document-based, No-SQL database used to store application data in a form of documents.

**ExpressJS:** A server side Web Application framework for Node.js.

**ReactJS:** A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.

**Node.js:** A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

Specifically, a simple **To-Do application** that creates To-Do will be deployed as a MERN stack in AWS Cloud. The procedure for this implementation is divided into two; 

**Step 1 – backend configuration:** At this sage i focus on the i installation of ''expressjs'', the Models and creation of the Mongodb database

**Step 2 – frontend creation:** This second step i will be creating a user interface for the Web client (browser) to interact with the application

## STEP 1 – BACKEND CONFIGURATION
After conecting the windows terminal to the EC2 instance created in the aws cloud, the ubuntu in use was upgraded using 


```
sudo apt upgrade
```

After the upgrade, i use the curl command to locate the Node.js software from Ubuntu repositories.

```curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -```

This was immediatly followed by the Installation of the Node.js with the command below;

```sudo apt-get install -y nodejs```

<img width="941" alt="image" src="https://user-images.githubusercontent.com/111741533/195948562-0ffe4074-6b20-4d8b-9c53-724fbb52da55.png">

with the successful installation of the nodejs server, i moved to the **application Code Setup**

The first thing to do was the creation of the ```Todo``` directory using 

```mkdir Todo```

Next, i used the command ```npm init``` to initialise the project, so that a new file named package.json will be created. This file will normally contain information about the application and the dependencies that it needs to run.

<img width="492" alt="image" src="https://user-images.githubusercontent.com/111741533/195949544-6234efe9-db6d-4966-aec7-359124b4a1dd.png">

#### Install ExpressJS

Here, i will install ExpressJs and create the Routes directory. The ```Expressjs``` helps to define routes of the application based on HTTP methods and URLs while the  ```routes```  will define various endpoints that the ```To-do`` app will depend on.
To use express, i installed it using npm:

```npm install express```

then i created a file index.js with the command 

```touch index.js```

The index.js file was then edited to add the following code snippets

```
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```



<img width="584" alt="image" src="https://user-images.githubusercontent.com/111741533/195951454-ddeed6a8-1479-469c-bb52-c8f8b5c067d0.png">

next i Installed the dotenv module before starting the node server.

<img width="614" alt="image" src="https://user-images.githubusercontent.com/111741533/195951917-1b62ab5a-1546-4719-af73-65255720de95.png">

The result showed that the server was running on port 5000, suggesting a need to edit the security group on the Ec2 to accomodate the custom port.

<img width="593" alt="image" src="https://user-images.githubusercontent.com/111741533/195952379-bd7d83e2-557b-45d1-91f9-331ae33e5f3a.png">

with this change on the inound settings, the server’s can now be accessed from the Public IP or Public DNS name followed by port 5000:

<img width="960" alt="project 3 1" src="https://user-images.githubusercontent.com/111741533/195952611-3cdbbaac-93e6-4086-aa6c-2ed50227be48.png">


****the route directory****
the route folder is created within the Todo folder

<img width="441" alt="image" src="https://user-images.githubusercontent.com/111741533/195953187-fe14d145-7b7b-4fea-a9ab-a2b2fc56f267.png">

with the ```route``` Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE


### MODELS

since the app is going to make use of Mongodb which is a NoSQL database, there is a need to create a model. The models  define the database schema while the schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database.

To create a Schema and a model, i installed mongoose which is a Node.js package that makes working with mongodb easier, then created aa new folder ```models``` . In the models directory i created a file and name it todo.js

<img width="382" alt="image" src="https://user-images.githubusercontent.com/111741533/195953851-3481dc1d-feb1-4f81-8571-0e8148357e94.png">

Open the file created with ``vim todo.js`` then add the code below in the file:

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;
```

Now we need to update our routes from the file api.js in ‘routes’ directory to make use of the new model.

In Routes directory, open **api.js** with ```vim api.js```, delete the code inside with :%d command and paste the code below into it then save and exit

<img width="578" alt="image" src="https://user-images.githubusercontent.com/111741533/195954391-1b60fb06-bb1c-4cdb-af34-35af84bd5d13.png">

### MONGODB DATABASE

in this app  i used MongoDB database as a service solution (DBaaS) to store data. For this,  i used a shared clusters free account in mongodb and selected AWS as the cloud provider which is ideal for this use case. after setting up the Mongodb, i allowed access to the MongoDB database from anywhere (Not secure, but it is ideal for testing).

<img width="959" alt="image" src="https://user-images.githubusercontent.com/111741533/195955109-7cfbe030-59f5-4a7f-a828-30eee3176b4d.png">


In the index.js file, i specified process.env to access environment variables, but i had not yet created this file. So i Created a file in the **Todo** directory and name it .env.

<img width="509" alt="image" src="https://user-images.githubusercontent.com/111741533/195955353-39717961-4dc7-4977-9414-4f753457f778.png">

the .env file was edited to add the connection string to access the database in it. The connection string was retrived from the mongodb.

<img width="575" alt="image" src="https://user-images.githubusercontent.com/111741533/195955639-a214aa6f-9a20-4bdc-b4bd-62b3212cd4ac.png">

after adding the database string, the **index.js** was updated to to reflect the use of **.env **. To open the file ```vim index.js```

the entire code in the index.js file was replaced with the following 

```
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then(() => console.log(`Database connected successfully`))
.catch(err => console.log(err));

//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
console.log(err);
next();
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```

After editing the file, its time to Start the server using the command:

```node index.js```

From its implementation however, i had the error 
```Error: listen EADDRINUSE: address already in use :::5000```. 

<img width="743" alt="image" src="https://user-images.githubusercontent.com/111741533/195956204-39e3d430-2472-4fc1-8682-acd4b5198139.png">


this promted me to locate the PID and terminate the processs. 
using the ```kill -9 <PID>`` code, after its implementation, the server error was eliminated

<img width="692" alt="image" src="https://user-images.githubusercontent.com/111741533/195956486-34cb21d4-7085-4708-a711-0feabb1b4766.png">


## STEP 2 – FRONTEND CREATION

Here i created a user interface for a Web client (browser) to interact with the application via API. To start out with the frontend of the To-do app, i used the ```npx create-react-app client``` command to scaffold our app.

Before testing the react app, there are some dependencies that need to be installed. These dependencies are  **concurrently** and **nodemon**.

<img width="609" alt="image" src="https://user-images.githubusercontent.com/111741533/195956883-610150b3-6a46-4276-9ba4-6f35336ab26e.png">


In Todo folder open the package.json file. and change the _scripts_ function to 

```
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```

Then i Configured the Proxy in package.json: To add the key value pair in the package.json file "proxy": "http://localhost:5000".

after the configuration, in the Todo directory run the app using  ```npm run dev```

<img width="745" alt="image" src="https://user-images.githubusercontent.com/111741533/195957377-59e4400c-2da5-4833-881d-46e0b3838ea9.png">






**Creating your React Components**

One of the advantages of react is that it makes use of components, which are reusable and also makes code modular. For our Todo app, there will be two stateful components and one stateless component

1) From the Todo directory go to the  src directory then ```mkdir components``` and in components ```touch Input.js ListTodo.js Todo.js```

<img width="828" alt="image" src="https://user-images.githubusercontent.com/111741533/195957565-327d7609-01df-4fd6-9191-80fc5064d914.png">

2) In the Input.js insert the following code snippet

```
import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {

state = {
action: ""
}

addTodo = () => {
const task = {action: this.state.action}

    if(task.action && task.action.length > 0){
      axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
          }
        })
        .catch(err => console.log(err))
    }else {
      console.log('input field required')
    }

}

handleChange = (e) => {
this.setState({
action: e.target.value
})
}

render() {
let { action } = this.state;
return (
<div>
<input type="text" onChange={this.handleChange} value={action} />
<button onClick={this.addTodo}>add todo</button>
</div>
)
}
}

export default Input
```

3) **Install Axios**

```npm install axios````

4) in the recently created ListTodo.js file nsert the following code snippet

```
import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {

return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}

export default ListTodo
```

5) Then in in  **Todo.js **file nsert the following code snippet
```
import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;
```


We need to make little adjustment to our react code. Delete the logo and adjust our App.js to look like this.
Move to the src folder to adjust the ```App.js```, ```App.css```, and the ```index.css```



a) in **App.jss** insert 
```
import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;
```

b) In **App.css** insert

```
.App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}

input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}

input:focus {
outline: none;
}

button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}

button:focus {
outline: none;
}

ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}

li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}

@media only screen and (min-width: 300px) {
.App {
width: 80%;
}

input {
width: 100%
}

button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}

@media only screen and (min-width: 640px) {
.App {
width: 60%;
}

input {
width: 50%;
}

button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}
```

c) in **index.css** insert 

ody {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}


Finally, from the **Todo directory** we run 

```npm run dev```

to launch the application

<img width="836" alt="terminal view launch" src="https://user-images.githubusercontent.com/111741533/195958517-07c76557-021d-4c2e-9027-6bbd26d50199.png">

app view from browser 

<img width="926" alt="image" src="https://user-images.githubusercontent.com/111741533/195958580-22249472-7a4f-4210-b3ed-b4f169ded215.png">







