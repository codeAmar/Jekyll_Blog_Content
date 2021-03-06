---
layout: post
title:  "Create Secure Tunnel To Node.js Application With Ngork"
date:   2018-02-24 15:07:59 -0800
categories: Development
author: matt
excerpt: "Ngrok is a tool to create secure URL to your localhost through any NAT or firewall. In simple language, it creates a secure Web URL for your localhost server - Tunnel - which can be assessed from anywhere."
---
 

 ![Article teaser]({{ "/assets/ngrok-teaser.png" | absolute_url }})
*"Ngrok",https://github.com/bubenshchykov/ngrok, BSD-2-Clause Copyright (c) 2018 bubenshchykov*



### Introduction
[Ngrok][ngrok-docs] is a tool to create **secure URL to your localhost through any NAT or firewall**. In simple language, it creates a secure Web URL for your localhost server - Tunnel - which can be accessed from anywhere.

### Use-cases
For many developers, Ngrok has become essential component of development. It empowers developers to accomplish following tasks:
- Demoing web sites to your client without deploying them.
- Working with webhooks in your development environment.
- Testing locally running mobile application online.
- Stable addresses for your field deployed electronic devices.
- Running your own cloud services.

### Advantages
These are the advantages of using ngrok over traditional approach:
- Available for all major platforms in the form of single binary with zero run-time dependency.
- Fast and Secure Tunnels.
- Traffic Request and Response inspection.
- TCP Tunnels and Password Protection.
- Shared account access for development team.
- Target Virtual-host sites
- Replaying requests.
- Multiple Simultaneous tunnels with single Ngrok client.
- Custom and Reserved sub-domains.
- Support for IP Whitelisting.
- Reserved TCP Adresses for integration with your hosted cloud services.
- End-to-End TLS Tunnels with your own SSL certificate and Private key.

### Disadvantages
Ngork doesn’t provide any temporary or permanent hosting solution. It only makes your application accessible to the Internet so that you can access, implement and test a new or existing feature while your back-end code is solely managed by you.


### Tutorial
In the following tutorial, we will install Ngrok in [Node][node-docs] environment, where we will expose our local server running with express to a secure public URL. In addition, we will also take look at the Password protection and Traffic inspection of tunnel.


- Firstly, you have to create a free account with [Ngrok][ngrok-account] to implement the additional functionality which we will explore later.

- After creating an account, you will be redirected to dashboard, where you can see various links on the side   panel such as:
    - Status – it shows the current active tunnels for your account.
    - Reserved – reserved Domains and reserved TCP are available for paid plans.
    - Auth – tunnel Authentication and IP Whitelist.
    - Team – share access with your team members and Google single sign-in.
    - Admin – credentials.
    - Billing – settings pertaining to plans and billing information.


- After selecting the Auth tab from the side panel, copy the line 
    
    ```sh
    
    ./ngrok authtoken <tokenhere>
    
    ```
someplace safe, we will need it later. Meanwhile, we will move ahead with the coding part.

- Now create a node project folder named `ngrok` in your computer.

- Open the terminal and cd into project folder, and type:
    
    ```sh
    
    Npm init
    
    ```
will initialize the current folder as node project.

- Once done with initialize, we will install modules required to run our project. So, type
    
    ```sh

    npm i -s dotenv express ngrok
    
    ```
this will install [express][express-docs], [dotenv][dotenv-docs] and [ngrok][ngrok-docs] as project dependency.

- Now create a file named `app.js`, all the back-end code will go inside this file.

  ```
  
  require('dotenv').config();

  const express = require('express');
  const app = express();
  const path = require('path');
  const ngrok = require('ngrok');
  const user = process.env.USER;
  const password = process.env.PASSWORD;

  app.get('/', (req, res) => {
      res.send('This is the tunnel created by Ngrok with Http Auth');
  });

  const server = app.listen(process.env.PORT, () => {
      console.log('Express listening at ', server.address().port);
  })

  ngrok.connect({
      proto : 'http',
      addr : process.env.PORT,
      auth : `${user}:${password}`
  }, (err, url) => {
      if (err) {
          console.error('Error while connecting Ngrok',err);
          return new Error('Ngrok Failed');
      } else {
          console.log('Tunnel Created -> ', url);
          console.log('Tunnel Inspector ->  http://127.0.0.1:4040');
      }
  });

  ```

- In order to save our credentials, we will use environment variable which can be easily managed by `dotenv` module – installed previously. You can find documentation [here][dotenv-docs]. So, create a file name `.env` in the root directory of project and copy the following content into it.
    
    ```sh

  PORT=5000
  USER=user
  PASSWORD=password
  
    ```
the above mentioned credentials will be entered in the browser to access our node application.

- Once you have saved both `app.js` and `.env` file. Head to the terminal and type following command into it:
    ```sh

  ./ngrok authtoken <tokenhere>
  
    ```
if you remember it is the same line which we copied from the ngrok account. Replace `<tokenhere>` will your personal token. After executing this command, it will connect our ngrok client with our online account for additional functionality.

- Now, we are ready to finally run our application. In the terminal type
    ```sh

  node app.js

    ```
it will generate two links – Tunnel Created and Tunnel Inspector.

  ![console image]({{ "/assets/ngrok-links.png" | absolute_url }})

- Copy both links and open them in individual browser tab. Tunnel Created link will show our node application 
  
    
    ![Browser image]({{ "/assets/ngrok-browser.png" | absolute_url }})
  

  and tunnel inspector will show all the traffic in the tunnel.

    ![Tunnel Inspection image]({{ "/assets/ngrok-inspect.png" | absolute_url }})

- Our credentials for node application would be `user` and `password`. 

- Finally, we can see running node application at a public secured URL, accessible on the internet.

- Now if we login to our ngrok account, we can see `tunnels online` under status tab.
  ![Ngrok account image]({{ "/assets/ngrok-account.png" | absolute_url }})


#### Source Code
You can find source code of tutorial => **[here][gist]** 

#### References 

*Bango,Rey. "How to Use ngrok to Test a Local Site." sitepoint. Accessed February 24, 2018.
https://www.sitepoint.com/use-ngrok-test-local-site/*

*"Documentation." ngrok. Accessed Feburary 24, 2018.
https://ngrok.com/docs*

*Herrera,Esteban. "Exposing your local Node.js app to the world." pluralsight. Accessed February 24, 2018.
https://www.pluralsight.com/guides/node-js/exposing-your-local-node-js-app-to-the-world*


[ngrok-docs]: https://ngrok.com/
[node-docs]: https://nodejs.org/en/
[ngrok-account]: https://dashboard.ngrok.com/user/signup
[express-docs]: https://expressjs.com/en/starter/installing.html
[dotenv-docs]: https://github.com/motdotla/dotenv
[gist]: https://github.com/codeAmar/Ngork-for-Node.js-Application
