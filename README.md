# freeCodeCamp-projects
solutions of some freeCodeCamp projects
## Contents
## [Front End Library Projects](https://github.com/BUB97/freeCodeCamp-projects/blob/master/README.md#front-end-library-projects)
+ [Markdown Previewer](https://github.com/BUB97/freeCodeCamp-projects/blob/master/README.md#2markdown-previewer)
## [APIs and Microservices Projects](https://github.com/BUB97/freeCodeCamp-projects/blob/master/README.md#apis-and-microservices-projects)
+ [Timestamp Microservice](https://github.com/BUB97/freeCodeCamp-projects/blob/master/README.md#1-timestamp-microservice)
+ [Request Header Parser Microservice](https://github.com/BUB97/freeCodeCamp-projects/blob/master/README.md#2-request-header-parser-microservice)
+ [URL Shortener Microservice](https://github.com/BUB97/freeCodeCamp-projects/blob/master/README.md#3-url-shortener-microservice)



## Front End Library Projects
### 2.Markdown Previewer
#### html
```html
<div id="root"></div>
```
#### JS(use react.js, react-dom.js, redux.js, react-redux.js, marked.js)
```javascript
const input = (text) => {
  return {
  type : 'input',
  text : text
  }
}

const inputReducer = (text = "<h1>Hello World</h1>",action) => {
  switch(action.type){
    case 'input':
      return marked(action.text);
    default:
      return marked(text);
  }
}

const store = Redux.createStore(inputReducer);

class Markdown extends React.Component {
  constructor(props){
    super(props);
    this.handleChange = this.handleChange.bind(this);
  }
  
  componentDidMount() {
    document.getElementById("preview").innerHTML = this.props.content;
  }
  
  componentWillReceiveProps(nextProps) {
    document.getElementById("preview").innerHTML = nextProps.content;
  }
  
  handleChange(event){
    this.props.show(event.target.value);
  }
  
  render(){
    return (
    <div>
        <textarea id="editor" onChange={this.handleChange} />
        <p id="preview" innerHTML={this.props.content}></p>
    </div>
    )
  }
}

const mapStateToProps = (state) => {
  return {
    content : state
  }
}

const mapDispatchToProps = (dispatch) => {
  return {
    show : function(text){
      dispatch(input(text))
    }
  }
}

const Container = ReactRedux.connect(mapStateToProps,mapDispatchToProps)(Markdown)

const Provider = ReactRedux.Provider;

class AppWrapper extends React.Component {
  constructor(props){
    super(props);
  }
  render(){
    return (
    <Provider store={store}>
    <Container />
    </Provider>
    )
  }
}

ReactDOM.render(<AppWrapper />,document.getElementById('root'));
```

## APIs and Microservices Projects
### 1. Timestamp Microservice
change "server.js"
```javascript
app.get("/api/timestamp/:date_string?",function(req,res){
  var date = req.params.date_string;
  if(!date){
    var dateObj = new Date();
    res.json({"unix":dateObj.getTime(),"utc":dateObj.toUTCString()});
  }
  else{
    if(/\d{5,}/.test(date)){
      var date = parseInt(date);
    }
    if((new Date(date)).toString() !== "Invalid Date"){
      var dateObj = new Date(date);
      res.json({"unix":dateObj.getTime(),"utc":dateObj.toUTCString()});
    }
    else{
      res.json({"error" : "Invalid Date" });
    }
  }
})
```
### 2. Request Header Parser Microservice
referrence: [Express documentatioin](http://www.expressjs.com.cn/4x/api.html#req)
```javascript
app.get("/api/whoami",function(req,res){
  res.json({"ipaddress":req.ip,"language":req.get('Accept-Language'),"software":req.get('User-Agent')});
  //you can also use req.acceptsLanguages() to get preffered languages
})
```
### 3. URL Shortener Microservice
#### Watch out the version of mongodb and mongoose dependencies (change "package.json")
the initial project use dependencies as:
```javascript
"dependencies": {
		"express": "^5.0.0-alpha.2",
		"mongodb": "^2.2.5",
		"mongoose": "^4.5.7",
		"cors": "^2.8.1",
		"body-parser": "^1.15.2"
	}
```
but I can't create documents in this way, so I change the dependencies version of mongodb and mongoose as earlier tutorials do, just as:
```javascript
"dependencies": {
		"express": "^5.0.0-alpha.2",
    "mongodb": "^3.0.0",
    "mongoose": "^5.6.5",
    "cors": "^2.8.1",
    "body-parser": "^1.15.2"
}
```
#### server.js
```javascript
'use strict';

var express = require('express');
var mongo = require('mongodb');
var mongoose = require('mongoose');
var Schema = mongoose.Schema;
var bodyParser =require('body-parser')
var dns = require("dns");
var cors = require('cors');
var app = express();

// Basic Configuration 
var port = process.env.PORT || 3000;

/** this project needs a db !! **/ 
// mongoose.connect(process.env.MONGOLAB_URI);
process.env.MONGOLAB_URI="mongodb+srv://zsh:lab401@cluster0-tbxkm.mongodb.net/test?retryWrites=true&w=majority";
mongoose.connect(process.env.MONGOLAB_URI);
var URISchema = new Schema({
  original_uri:{type:String,required:true},
  short_uri:{type:Number}
});
var URI = mongoose.model("URI",URISchema);
app.use(cors());

/** this project needs to parse POST bodies **/
// you should mount the body-parser here
app.use(bodyParser.urlencoded({ extended: false }))
//parse application/json
app.use(bodyParser.json())
app.use('/public', express.static(process.cwd() + '/public'));

app.get('/', function(req, res){
  res.sendFile(process.cwd() + '/views/index.html');
});
app.post("/api/shorturl/new",function(req,res){
  var uriString = req.body.uri;
  dns.lookup(uriString,(err,address,family)=>{
    if(address != undefined){
      var date = new Date();
      var timeStamp = date.getTime();
      URI.create({original_uri:uriString,short_uri:timeStamp},function (err, data) {
        if (err) return console.error(err);
          res.json({original_uri:uriString,
          short_uri:timeStamp});
      })
    }   
    else{
      res.json({"error":"invalid URL"});
    }
  })
});
// your first API endpoint... 
app.get("/api/hello", function (req, res) {
  res.json({greeting: 'hello API'});
});


app.listen(port, function () {
  console.log('Node.js listening ...');
});
```
