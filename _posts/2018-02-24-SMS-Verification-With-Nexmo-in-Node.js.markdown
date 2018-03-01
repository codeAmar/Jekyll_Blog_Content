---
layout: post
title:  "SMS Verification With Nexmo in Node.js"
date:   2018-02-27 15:07:59 -0800
categories: Development
author: matt
excerpt: "Nexmo provide genuine validating API built with 2FA technology, which allows you to send a PIN by SMS and phone to authenticate a user, whom can be contacted by specific number. Nexmo Verify API allows us to implement aforementioned functionality in our application."
---
 

 ![Article teaser]({{ "/assets/teaser-nexmo.jpg" | absolute_url }})
*"Authentication",https://pixabay.com/en/finger-fingerprint-security-digital-2081169/,CC0 Creative Commons*



### Introduction
Authentication is a fundamental requirement for a full stack application. It allows users and process to access resources by verifying their identity. In addition to password authentication, we can implement sms authentication to take additional security measure. It helps in protecting user privacy and data. Now a days most of the giant companies are implementing two factor authentication in both, web and mobile applications.


### What is Nexmo?
[Nexmo][nexmo-docs] provide genuine validating API built with [2FA][2fa-docs] technology, which allows you to send a PIN by SMS and phone to authenticate a user, whom can be contacted by specific number. Nexmo Verify API allows us to implement aforementioned functionality in our application.


### Advantages
These are the following advantages of using nexmo for sms authentication:
- Global and long-lasting, as almost every person has one phone number which they retain for decades.
- Phone numbers are really extensive and time-consuming to fake.
- Protection from choosing commonly dangerous passwords for multiple online accounts.
- Validity of PIN code for 5 minutes.
- Protects brand reputation.
- Ensues trust among users.
- Easy implementation.
- Adaptive Routing.
- Direct-to-carrier

### Disadvantages
These are the following disadvantages of using nexmo:
- Hinderence with advance protocols and telecom infrastructure in specific part of globe.
- Sms authentication costs.
- Mandatory to set up a regional number, before you can offer sms authentication service to your clients.

### Requirements
These are the following parameters we have to retrieve from nexmo account in order to make our application work:
- API KEY
- API SECRET
- NEXMO_FROM_NUMBER


### Restrictions
We are currently implementing sms authentication for Canadian mobile numbers only, as we picked our number in canada. If you are looking for implementation of this feature, you have to buy number in that region for it to work. Thats the only restriction with Nexmo.


### Tutorial
This tutorial will demonstrate the implementation of sms authentication with Nexmo in Node.js application:

- Firstly, we have to create an online [account][nexmo-account] at nexmo website in order to get `API Key`, `API secret` and `nexmo number` for our application to work.

- Once you have created account, you can buy a number relevant to your region in which you want to implement this functionality. This number would be the one from which all the PIN code text will be sent to the users.

 ![Article teaser]({{ "/assets/nexmo-account.png" | absolute_url }})

- Now, create a project folder named `nexmo`.

- Open the terminal and cd into the project folder and type:
    ```sh

    npm init
    
    ```
to initialize it as [node][node-docs] project.

- After that, install the dependencies required. Open the terminal and cd into the project folder and type:
    ```sh

    npm i -s body-parser cookie-parser dotenv ejs express nexmo
    
    ```
- Previous two steps will create a `node_modules` directory and `package.json` file, in which all the dependencies are listed.

    ![Article teaser]({{ "/assets/project-dependencies.png" | absolute_url }})


- Now add multiple files and folder in such a way that our final project folder `nexmo` looks like this:

    ![Article teaser]({{ "/assets/project-folder.png" | absolute_url }})

- Now, we will start adding code into the files which we have already created. So, paste these styles into the file named `style.css`.

    ```sh

    body{
      background-color:white;
    }

    body > div{
      margin: auto;
      margin-top: 20%;
    }

    h3{
      text-align: center;
    }

    ```

- Now, we will start putting code inside the `routes` folder file’s

    ```sh
    
    #=> index.js
    
    const express = require('express');
    const router = express.Router();
    
    
    /* GET home page. */
    router.get('/', function(req, res, next) {
      res.render('index');
    });
    
    module.exports = router;
        
    ```
    ```sh
    
    #=> register.js
    
    require('dotenv').config()
    const express = require('express');
    const router = express.Router();
    const Nexmo = require('nexmo');
    
    const nexmo = new Nexmo({
      apiKey: process.env.API_KEY,
      apiSecret: process.env.API_SECRET
    });
    
    
    router.post('/register', (req, res) => {
      // A user registers with a mobile phone number
      let phoneNumber = req.body.number;
      let message = req.body.message;
      
      console.log(phoneNumber);
      nexmo.verify.request({number: phoneNumber, brand: message}, (err, result) => {
        if(err) {
          //res.sendStatus(500);
          res.render('status', {message: 'Server Error'});
        } else {
          console.log(result);
          let requestId = result.request_id;
          if(result.status == '0') {
            res.render('verify', {requestId: requestId});
          } else {
            //res.status(401).send(result.error_text);
            res.render('status', {message: result.error_text, requestId: requestId});
          }
        }
      });
    });
    
    
    module.exports = router;

    ```
    
    ```sh
    
    #=>status.js
    
    const express = require('express');
    const router = express.Router();
    
    
    /* GET home page. */
    router.get('/status', function(req, res, next) {
      res.render('status');
    });
    
    module.exports = router;
    

    ```
    ```sh
    
    #=>verify.js

    require('dotenv').config()
    const express = require('express');
    const router = express.Router();
    const Nexmo = require('nexmo');
    
    const nexmo = new Nexmo({
      apiKey: process.env.API_KEY,
      apiSecret: process.env.API_SECRET
    });
    
    router.get('/verify',(req,res)=>{
      res.render('verify');
    });
    
    
    router.post('/confirm', (req, res) => {
      // Checking to see if the code matches
      let pin = req.body.pin;
      let requestId = req.body.requestId;
    console.log('value of requestid in verify post handler is ' + requestId);
      nexmo.verify.check({request_id: requestId, code: pin}, (err, result) => {
        if(err) {
          //res.status(500).send(err);
          res.render('status', {message: 'Server Error'});
        } else {
          console.log(result);
          // Error status code: https://docs.nexmo.com/verify/api-reference/api-reference#check
          if(result && result.status == '0') {
            //res.status(200).send('Account verified!');
            res.render('status', {message: 'Account verified! 🎉'});
          } else {
            //res.status(401).send(result.error_text);
            res.render('status', {message: result.error_text, requestId: requestId});
          }
        }
      });
    });
    
    
    module.exports = router;
    
    ```
    
- Now, we will start putting code inside the `views` folder file’s
    
    ```sh
    
    #=>error.ejs

    <h1><%= message %></h1>
    <h2><%= error.status %></h2>
    <pre><%= error.stack %></pre>
    
    ```
    
    ```sh
    
    #=>index.ejs
    
    <!DOCTYPE html>
    <html>
      <head>
        <title>Index</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css">
        <link rel='stylesheet' href='/stylesheets/style.css' />
      </head>
      <body>
          <div class="form-group col-md-8 ">
              <h3>Send a Message</h3>   
              <form action="/register" method="post">
                  <div class="form-group row">
      
                      <label for="example-text-input" class="col-2 col-form-label">Text</label>
                      <div class="col-10">
                          <input class="form-control" type="text" placeholder="Wassup" name="message"     id="example-text-input">
                      </div>
                  </div>
      
      
                  <div class="form-group row">
                      <label for="example-tel-input" class="col-2 col-form-label">Telephone</label>
                      <div class="col-10">
                          <input class="form-control" type="tel" placeholder="1-(555)-555-5555" name = "number"     id="example-tel-input">
                      </div>
                  </div>
                  <button class="btn btn-lg btn-primary btn-block" type="submit">Send</button>
      
              </form>
      
          </div>
      
      </body>
      <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>
    
    </html>
    
    ```
    ```sh
    
    #=>status.ejs
    
    <!DOCTYPE html>
    <html>
      <head>
        <title>Status</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css">
        <link rel='stylesheet' href='/stylesheets/style.css' />
      </head>
      <body>
          <section id="main" role="main">
              <p class="status">
                <% if(message){ %>
                 <%= message %>
                <% } %> </p>
              <% if(locals.requestId) { %>
                Try with another PIN if you have one.<br>
                <form action="/confirm" method="post">
                  <div class="fa fa-key"></div>
                  <input type="number" name="pin" placeholder="1234" required>
                  <input type="hidden" name="requestId" value="<%= requestId %>" >
                  <input type="submit" value="Verify PIN">
                </form>
              <% } %>
          
            </section>
      </body>
      <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>
    </html>
    
    ```
    ```sh
    
    #=>verify.ejs
    
    <!DOCTYPE html>
    <html>
      <head>
        <title>verify</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css">
        <link rel='stylesheet' href='/stylesheets/style.css' />
      </head>
      <body>
        <form action="/confirm" method="post">
          <input type="tel" name="pin" placeholder="1234" required>
          <input type="hidden" name="requestId" value="<%= requestId %>" >
          <input type="submit" value="Verify PIN">
      </form>
      
      </body>
      <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>
    </html>
    
    ```
    
- Now, we will create `.env` file which contains [environment variables][env-docs] for our node application.
    
    ```sh
    
    API_KEY=enter_personal_key_here
    API_SECRET=enter_secret_key_here
    NEXMO_FROM_NUMBER=enter_nexmo_phone_number_here
    
    ```
    
- After we are done with `.env` file, we will create file named `app.js`, and copy the content into it
    
    ```sh
    
    require('dotenv').config()
    var express = require('express');
    var path = require('path');
    var cookieParser = require('cookie-parser');
    var bodyParser = require('body-parser');
    
    var index = require('./routes/index');
    var register = require('./routes/register');
    var verify = require('./routes/verify');
    var status = require('./routes/status');
    
    var app = express();
    
    
    // view engine setup
    app.set('views', path.join(__dirname, 'views'));
    app.set('view engine', 'ejs');
    
    // uncomment after placing your favicon in /public
    //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
    app.use(bodyParser.json());
    app.use(bodyParser.urlencoded({ extended: false }));
    app.use(cookieParser());
    app.use(express.static(path.join(__dirname, 'public')));
    
    app.use(index);
    app.use( register);
    app.use( verify);
    app.use( status);
    
    const server = app.listen(process.env.PORT || 5000, () => {
      console.log('Express server listening on port %d in %s mode', server.address().port, app.settings.env);
    });
    
    module.exports = app;
    
    ```
    
- We are done with the coding, in order to run the application type:
    
    ```sh
    
    node app.js
    
    ```
in the terminal and open the browser to http://localhost:5000/
    
- The page would look like this
    

   ![Article teaser]({{ "/assets/landing-page.png" | absolute_url }})
    

- Once you enter the message and the number you want to send to, with country code as prefix, click send. It will     open 
    

   ![Article teaser]({{ "/assets/send-pin.png" | absolute_url }})
    

- Now, enter the 4 digit PIN code sent to your mobile and click verify pin.
    
   ![Article teaser]({{ "/assets/account-verified.png" | absolute_url }})

- If the PIN matches with one sent to mobile, it will be redirected to success page.

   ![Article teaser]({{ "/assets/pin-mobile.jpg" | absolute_url }})


- This commences our tutorial.


### Source Code
You can find source code of tutorial => **[here][gist]** 

#### References 

*"Discover how we deliver quality." nexmo. Accessed February 28, 2018.
https://www.nexmo.com/why-nexmo/*

*"Verify API Overview." nexmo. Accessed Feburary 28, 2018.
https://developer.nexmo.com/verify/overview*

*Srinivasan,Srivatsan. "Phone Number Verification: A Stronger Alternative to Traditional Passwords
." nexmo. Accessed February 28, 2018. https://www.nexmo.com/blog/2017/03/09/phone-number-verification-a-stronger-alternative-to-traditional-passwords/*

[nexmo-docs]: https://www.nexmo.com/
[2fa-docs]: https://en.wikipedia.org/wiki/Multi-factor_authentication
[nexmo-account]: https://dashboard.nexmo.com/sign-up
[node-docs]: https://nodejs.org/en/docs/
[env-docs]: https://www.npmjs.com/package/dotenv
[gist]: https://github.com/codeAmar/Two-Factor-Authentication-With-Nexmo-For-Node.js


