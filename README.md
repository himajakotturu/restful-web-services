# restful-web-services
To implement a restful web service using express js and support GET, POST, PUT and DELETE operations
//require node modules
var MongoClient=require('mongodb').MongoClient; //Mongodb module
ObjectID=require('mongodb').ObjectID;
var assert=require('assert');//assert will help us in handling the errors

//express is used to develop restful webservices
var express=require('express');
var bodyparser=require('body-parser');//its a module
var app=express();
//suppose if we are using forms then using multer will be helpful
var multer=require('multer');//its a module
var upload=multer();//for parsing multipart

app.use(bodyparser.json());//for parsing json object
app.use(bodyparser.urlencoded({extended:true}));


const dbName = 'test';// Database Name
var url = 'mongodb://localhost:27017'; // connection URL
// Use connect method to connect to the server
       MongoClient.connect(url, (err, client) => {
       var db = client.db(dbName);
       app.get('/user',function(req,res){ 
           db.collection('customers').find({}).toArray(function(err, result) {
                if(err) throw err;
                res.json(result);
            });
       });
        


        app.post('/user',function(req,res){
             db.collection("customers").insertOne(req.body,function(err,result){
                console.log(JSON.stringify(result));
                res.json(result);
             })
        });
        
        //getting only item
        app.get('/user/:id',function(req,res){
            //console.log(req.params.id);
            var objectId=new ObjectID(req.params.id);
            var obj={"_id":objectId};//encapsulation
            db.collection("customers").find(obj).toArray(function(err,result){
                if(err){
                    res.status(500).send(JSON.stringify(err));
                }
                //console.log(JSON.stringify(result));
                res.json(result);
            });
        })

     //updating the existing record
         app.put('/user/:id',function(req,res){
            //console.log(req.params.id);
            var objectId=new ObjectID(req.params.id);
            var obj={"_id":objectId};//encapsulation
            db.collection("customers").find(obj,req.body,function(err,result){
                if(err){
                    res.status(500).send(JSON.stringify(err));
                }
                //console.log(JSON.stringify(result));
                res.json(result);
            });
        });

     app.delete('/user/:id',function(req,res){
        //console.log(req.params.id);
        var objectId=new ObjectID(req.params.id);
        var obj={"_id":objectId};//encapsulation
        db.collection("customers").remove(obj, function(err, result){
            if(err){
                res.status(500).send(JSON.stringify(err));
            }
            res.json(result);
        });
    })

       // Close the DB
    //client.close();
});

    
app.listen(3000,function(){
    console.log('The server is running at http://localhost:3000')
})
 
   
