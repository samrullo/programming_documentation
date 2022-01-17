# Node

## Quick start

```javascript
const fs = require("fs")
fs.copyFileSync("sourceFile","destinationFile")
```

```node``` comes with REPL(Read Evaluate Print Loop), which means you can launch interactive command line and run node commands and see results interactively

## npm

npm is a node package manager

To initialize a node package

```bash
npm init
```

To install node package

```bash
npm install superheroes
```

When you install a package it gets added to ```dependencies``` in package.json.

Once you install a package you can use it as below

```javascript
const superheroes = require("superheroes");
console.log(superheroes.random());
```

## Express

Express is a node framework that helps you build web applications with less effort.

```javascript
const express = require("express");
const app = express();

app.get("/",(req,res)=>{
  // multiple ways to generate html
  res.send("<h1>Hello there</h1>") // send html
  res.sendFile(__dirname + "index.html") // send html file, __dirname is current file location
  res.render("home") // render ejs template called home.ejs
})
app.listen(process.env.PORT, ()=>{console.log("Started server on port "+process.env.PORT)})
```

When you start your node application in development, you usually want to use ```nodemon``` as it can refresh and restart your app every time you make changes.

```bash
nodemon app.js
```

## Access form elements with body-parser

```body-parser``` is a node package that helps to access form fields seamless.

```bash
npm install body-parser
```



Suppose you have a form like below

```html
<form action="/add" method="post">
  <input type="number" name="number1"/>
  <input type="number" name="number2"/>
  <button type="submit">Add</button>
</form>
```

We can process above form with below code

```javascript
const express = require("express");
const bodyParser = require("body-parser");

const app = express();
app.use(
  bodyParser.urlencoded({
    extended: true,
  })
);

app.post("/add",(req,res)=>{
  const number1=req.body.number1;
  const number2=req.body.number2;
  const result=number1+number2;
  res.send("The answer is " + result);
})

app.listen(process.env.PORT, ()=>{console.log("Started server on port "+process.env.PORT)})
```

## API quick intro

- Endpoint : API url
- Paths : It is a path to various resources, usually follows API Endpoint
- Parameters : parameters to pass when using API
- Authentication : authenticate the user using API

For example an endpoint https://api.kanye.rest/ returns random quotes.

https://sv443.net/jokeapi/v2/joke is an API endpoint for producing jokes. You can specify a path of ```Programming``` to get jokes related to programming.

The below combination of Endpoint and Path produces a joke like below

https://sv443.net/jokeapi/v2/joke/Programming

```json
{
    "error": false,
    "category": "Programming",
    "type": "twopart",
    "setup": "What is a dying programmer's last program?",
    "delivery": "Goodbye, world!",
    "flags": {
        "nsfw": false,
        "religious": false,
        "political": false,
        "racist": false,
        "sexist": false,
        "explicit": false
    },
    "id": 56,
    "safe": true,
    "lang": "en"
}
```

You can pass a parameter to this API path to get jokes that contain string debugging in it

https://sv443.net/jokeapi/v2/joke/Programming?contains=debugging

```json
{
    "error": false,
    "category": "Programming",
    "type": "single",
    "joke": "Debugging: Removing the needles from the haystack.",
    "flags": {
        "nsfw": false,
        "religious": false,
        "political": false,
        "racist": false,
        "sexist": false,
        "explicit": false
    },
    "id": 40,
    "safe": true,
    "lang": "en"
}
```

When the API is used by large number of users the developers have to start thinking about limiting number of accesses to prevent the server from crashing or getting slow. To identify the developer using the API and to track how many times the developer is using the API, API provider issues identifier called API key, or API token. Later API provider can use this API key to charge developer or the users of the API.

```https://samples.openweathermap.org/data/2.5/weather?q=London,uk&appid=<API key>```

```Postman for Mac``` which can be downloaded from ```postman.com/downloads`` helps to ease interaction with APIs

JSON (Javascript Object Notation) is slightly different from javascript objects, although they are very similar. In JSONs usually both keys and values are wrapped in quotations.

```JSON Viewer Awesome``` chrome extension helps to visualize API JSON responses in pretty format.

### How to get request to external server node

We can use ```https``` module to make requests to external servers hosting APIs

```javascript
const https = require("https");

https.get(url,(api_response)=>{
  api_response.on("data",(api_data)=>{
    const weatherData = JSON.parse(api_data);
    console.log(weatherData);
    
    const temp = weatherData.main.temp;
    const weatherDesc = weatherData.weather[0].description;
    
    console.log("temperature is "+temp)
  })
})
```

