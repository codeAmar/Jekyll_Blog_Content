---
layout: post
title:  "Manage Payments by Using Stripe API for your Next E-commerce Business"
date:   2018-01-22 23:07:59 -0800
categories: Development
author: matt
excerpt: "Stripe is best option as it includes features like robustness, cleanliness, internationalization and flexibility. Although they charge you for doing their part in the transaction, but the scource code generated after using stripe in your application is elegant and modular."
---
 

 ![Article teaser]({{ "/assets/stripe-teaser.png" | absolute_url }})
*"Stripe.js",https://stripe.com/ca, used under CC / Derivative of original*


### What is Stripe?
If you ever find yourself in need of a payment API for your next e-commerce business, [Stripe][stripe-docs] is best option as it includes features like robustness, cleanliness, internationalization and flexibility. Although they charge you for doing their part in the transaction, but the scource code generated after using stripe in your application is elegant and modular.

### Tutorial
In this tutorial, we will be using [Node.js][node-docs] for making the api calls from our application to stripe.js. The server used for node will be [Express][express-docs] and; [pug][pug-docs] as templating engine.

**The general interaction of user with the application will be:**
- User enter the amount they want to pay.
- Stripe loaded form is shown to the user, which asks user for payment credentials.
- Once submitted, the user will be routed to confirmation page. Otherwise it will show relevant error pertaining to the transaction.


**These are the following steps of tutorial:**


- Before we begin with the stripe, we have to make sure node.js is installed in the system locally.


- You can find the installation instructions [here][node-install].


- In addition to node.js, your system should also have [npm][npm-docs] installed locally. 


- In order to make our application access the services provided by stripe.js, we have to get `PUBLISHABLE_KEY` and `SECRET_KEY` from stripe API dashboard. This can be retrieved after making an [account][stripe-account] on official website and verifying your email. 


- Once you have both values from stripe dashboard, we can move forward by creating our project folder named `my_stripe`.


- Inside `my_stripe` folder, create a file `package.json` and copy the following content into it.


    ```sh
#=>package.json
{
  "name": "my_stripe",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "node app.js",
    "test": "mocha"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "body-parser": "^1.18.2",
    "dotenv": "^4.0.0",
    "express": "^4.16.2",
    "pug": "^2.0.0-rc.4",
    "stripe": "^5.4.0"
  },
  "devDependencies": {
    "chai": "^4.1.2",
    "chai-http": "^3.0.0",
    "mocha": "^5.0.0"
  }
}
    ```


- Now, open the terminal and cd into `my_stripe` folder; and type the following commands.


    ```sh
Npm install
```
	It will install all the dependencies mentioned in ‘package.json’ file, created previously.



- In addition to that create three seperate folders - `public` , `test` and `views` inside the `my_stripe` folder. After that, create multiple files and folder inside these folder as mentioned, so that the final project directory structure like:


    ```sh
my_stripe->
    node_modules
    public->
      css->
        main.css
      images->
        stripe.png
    test->
      test.js
    views->
      charge.pug
      index.pug
    app.js
    .env
    package.json
    ```
*node_modules folder will be automatically populated when you run the command ‘npm install’ mentioned in previous step.*


- Type the following styles in `main.css` file. These are the styles used for both pug templates.


      body {
        padding-top: 40px;
        padding-bottom: 40px;
        background-color: #eee;
      }

      .form-signin {
        max-width: 330px;
        padding: 15px;
        margin: 0 auto;
      }

      .form-signin .form-signin-heading,
      .form-signin .checkbox {
        margin-bottom: 10px;
      }

      .form-signin .checkbox {
        font-weight: normal;
      }

      .form-signin .form-control {
        position: relative;
        height: auto;
        -webkit-box-sizing: border-box;
        box-sizing: border-box;
        padding: 10px;
        font-size: 16px;
      }

      .form-signin .form-control:focus {
        z-index: 2;
      }

      .form-signin input[type="email"] {
        margin-bottom: -1px;
        border-bottom-right-radius: 0;
        border-bottom-left-radius: 0;
      }

      .form-signin input[type="password"] {
        margin-bottom: 10px;
        border-top-left-radius: 0;
        border-top-right-radius: 0;
      }

      .stripe-img {
        max-width: 20%;
        height: auto;
      }

      .approved-text {
        color: green;
        margin: 3% auto;
      }

      .approved-para {
        margin-top: 2%;
      }


- Moving in the order of directory structure mapped before, you can find the stripe.png below:


    ![Stripe image]({{ "/assets/stripe.png" | absolute_url }})
	
  
  *please download the image by right clicking it and selecting the option of ‘save image as’*


- Inside the `test.js`, paste the following code:



      const server = require('../app.js')
      const chai = require('chai');
      const chaiHttp = require('chai-http');
      const expect = chai.expect;
      const should = chai.should();

      chai.use(chaiHttp);

      describe('Main Page Testing', function () {
          it('should validate if path `\index` route is working', function (done) {
              chai.request(server)
                  .get('/')
                  .end(function (err, res) {
                      res.should.have.status(200)
                      done()
                  })
          })
      })



- Now, open the `charge.pug` file and paste the following code:


    ```sh
html
  head
    title Stripe Payment API
    script(src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous")
    link(rel="stylesheet", href="/css/main.css")
    link(rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous")
  body
    div(class="text-center mb-4")
      h4(class="approved-text") Approved
      img(src="/images/stripe.png", alt="Stripe API" class="stripe-img")
      p(class="approved-para") You successfully paid <strong>$#{amount}</strong>
      
    ```
*please be carefull with the indentation as this is .pug file, due to which space and tabs matters.*

- Next, open the `index.pug` and copy the following code:


    ```sh
html
  head
    title Stripe Payment API
    script(src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous")
    link(rel="stylesheet", href="/css/main.css")
    link(rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous")
  body
    div(class="container")
    form(action="/charge", method="post", class="form-signin")
      article
        h2(class="form-signin-heading") Enter Amount
        label(for="inputAmount" class="sr-only") Amount
        input(type="number" step="any" id="inputAmount" name="amount" class="form-control" placeholder="5.00" required autofocus)
        script(
        src="//checkout.stripe.com/v2/checkout.js",
        class="stripe-button",
        data-key=keyPublishable,
        data-locale="auto",
        data-description="Sample Charge")

    ```


- Now, open the `.env` file – there’s a dot in the front- and paste the following code into it:


    ```sh
PUBLISHABLE_KEY=<enter the your publishable key here, retrieved from stripe dashoard>
SECRET_KEY=<enter the your secret key here, retrieved from stripe dashoard>
    ```


- After that, open `app.js` inside the `my_stripe` folder and paste the following code inside it.


      require('dotenv').config()
      const keyPublishable = process.env.PUBLISHABLE_KEY;
      const keySecret = process.env.SECRET_KEY;
      const express = require('express');
      const app = express();
      const path = require('path');
      const stripe = require("stripe")(keySecret);

      app.set("view engine", "pug");
      app.use(require("body-parser").urlencoded({extended: false}));

      app.use(express.static(path.join(__dirname,'public')));
      app.get("/", (req, res) =>
        res.render("index.pug", {keyPublishable}));

      app.post("/charge", (req, res) => {
        let amount = parseFloat((req.body.amount)*100);
        console.log(req.body);
        stripe.customers.create({
           email: req.body.stripeEmail,
          source: req.body.stripeToken
        })
        .then(customer =>
          stripe.charges.create({
            amount,
            description: "Sample Charge",
               currency: "usd",
               customer: customer.id
          }))
        .then(charge => res.render("charge.pug",{amount: (amount/100)}));
      });

      app.listen(4567);

      module.exports = app;



- Once you are finished with creation and code insertion of all the files. Head to the terminal and cd inside the `my_stripe` folder and type.


    ```sh
npm start
    ```

- Then, open your browser and enter the following address in the URL:


    ```sh
http://localhost:4567/charge
    ```

- Now, our application is running successfully. 


- If you would like to perform test on the application, type. 


    ```sh
npm test
    ```
in the terminal and experience tests passing with green color.





#### Source Code
You can find source code of tutorial => **[here][gist]** 

#### References 

*"Using Checkout and Express." stripe. Accessed January 22, 2018.
https://stripe.com/docs/checkout/express*

*"Attributes." pugjs. Accessed January 22, 2018.
https://pugjs.org/language/attributes.html*

*Dorfman,Justin. "BootstrapCDN." bootstrapcdn. Accessed January 22,2018.
https://www.bootstrapcdn.com/*


[stripe-docs]: https://stripe.com/ca
[node-docs]: https://nodejs.org/en/
[express-docs]: https://expressjs.com/
[pug-docs]: https://pugjs.org/api/getting-started.html
[node-install]: https://nodejs.org/en/download/
[npm-docs]: https://www.npmjs.com/get-npm?utm_source=house&utm_medium=homepage&utm_campaign=free%20orgs&utm_term=Install%20npm
[stripe-account]: https://dashboard.stripe.com/register
[gist]: https://github.com/codeAmar/Stripe-API-with-Node.js
