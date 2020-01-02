# freeCodeCamp-projects
solutions of some freeCodeCamp projects

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
