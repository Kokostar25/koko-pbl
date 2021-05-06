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

### In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.
` "scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},`





