# freeCodeCamp-projects
solutions of some freeCodeCamp projects

## APIs and Microservices Projects
### 1. Timestamp Microservice
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
