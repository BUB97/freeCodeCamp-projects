# freeCodeCamp-projects
solutions of some freeCodeCamp projects

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
### 2.Request Header Parser Microservice
referrence: [Express documentatioin](http://www.expressjs.com.cn/4x/api.html#req)
```javascript
app.get("/api/whoami",function(req,res){
  res.json({"ipaddress":req.ip,"language":req.get('Accept-Language'),"software":req.get('User-Agent')});
  //you can also use req.acceptsLanguages() to get preffered languages
})
```
### 3.URL Shortener Microservice

