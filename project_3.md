# Simple To-Do application on MERN Web Stack
## Backend configuration
### Ubuntu Update and Upgrade
- sudo apt update
- sudo apt upgrade
### get the location of Node.js software from Ubuntu repositories.
- curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
### Install Node.js on the server
- sudo apt-get install -y nodejs
### Verify the node/npm installation with the command 
- node -v 
- npm -v
## Application Code Setup
### Create a new Directory for To-do Project
- mkdir Todo
- cd Todo
- npm init ( to initialize your project, to bring up a new file package.json which will contain information about the application and  dependencies)
## Install ExpressJS
- npm install express
- touch index.js
## Install the dotenv module
- npm install dotenv
- vim index.js
## open port 5000
## Open your terminal in the same directory as your index.js file and type:
- node index.js
- http://54.85.249.45:5000
![image](https://user-images.githubusercontent.com/70109786/116919454-e66b9400-ac16-11eb-8321-e203dc703113.png)
## Routes
### Our TO-do app needs to perform 3 action
1. Create a new task
2. Display list of all tasks
3. Delete a completed task
### Create routes folder
- cd routes
### Create File api.js
- touch api.js
- vim api.js
### copy code 
`const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;`

## Models - We use Models to define database schema. We need to install mongoose to create the Schema and Model
- cd Todo 
- npm install mongoose
- mkdir models 
- cd models 
- touch todo.js
- vim todo.js
### paste code
'const mongoose = require('mongoose');
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

module.exports = Todo;'
### Now we need to update our routes directory
- vim api.js
### delete previous code %d
### paste new code  
`const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;`
## MongoDB Database
### Create a MongoDB database and collection inside mLab
###Create a file in your Todo directory and name it .env
- touch .env
- vi .env
### paste in this connection string with usename, pw and database name
- DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
### Update the index.js to reflect the use of .env, to enable node.js connect to the database
- vim index.js
- %d
### paste code
  
`const express = require('express');
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
});`

### start server using 
- node index.js
### Server running on port 5000 Database connected successfully
## Testing Backend Code without Frontend using RESTful API
### Use Postman to test our API endpoints. 
### Create a POST GET and DELETE requests.
### Post Request
![image](https://user-images.githubusercontent.com/70109786/117243163-4a9b7d00-adfc-11eb-8fad-c1fbd19b5fce.png)

### Get Request
![image](https://user-images.githubusercontent.com/70109786/117243193-5c7d2000-adfc-11eb-9a16-235bf49ef7d7.png)

### Delete Request
![image](https://user-images.githubusercontent.com/70109786/117243239-7454a400-adfc-11eb-8af6-c287e7be2c81.png)

## Step 2 - Frontend creation
### Time to create a user interface for a Web client (browser) to interact with the application via API, using the create-react-app
### in Todo Directory run 

### Which will create a todo directory called client, where the react code will be added

### Running a React App
#### Before testing the react app, there are some dependencies that need to be installed.
- npm install concurrently --save-dev
- $ npm install nodemon --save-dev

### In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.
` "scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},`

### Configure Proxy in package.json
- cd client
- vi package.json
### Add the key value pair in the package.json file "proxy": "http://localhost:5000".

- npm run dev
### The app should open and start running on localhost:3000, so open port 3000 to access the application

![image](https://user-images.githubusercontent.com/70109786/117397891-c4963980-aec2-11eb-8927-88e827d3ec8a.png)


### Creating your React Components

- cd client 
- cd src
- mkdir components
- cd components
### inside component create three file input.js, ListTOdo.js and Todo.js
- vi Input.js 
### PASTE CODE
`import React, { Component } from 'react';
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

export default Input`

### To make use of a Promise based HTTP client for the browser and node.js install axios
### change directory to client
- cd ../.. 
- npm install axios
- cd src/components
- vi ListTodo.js
### Paste code

`import React from 'react';

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

export default ListTodo`

### Then open Todo.js and paste code 

`import React, {Component} from 'react';
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
}`

export default Todo;`

### We need to make little adjustment to our react code. Delete the logo and adjust our App.js to look like this.
### Move to the src folder
- vi App.js
### Paste code

`import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;`
### In the src directory open the App.css

- vi App.css


### Paste code

`.App {
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

### In the src directory open the index.css

- vim index.css


### Paste code

body {
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

Go to the Todo directory

npm run dev
### App is up and running. if you send test request via postman you'll see it reflect on the web browser app.
![image](https://user-images.githubusercontent.com/70109786/117400361-0aa1cc00-aec8-11eb-8a04-428aaeb169fa.png)
