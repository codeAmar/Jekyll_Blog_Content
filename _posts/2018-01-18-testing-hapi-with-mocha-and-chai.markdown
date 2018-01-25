---
layout: post
title:  "Testing Hapi with Mocha and Chai"
date:   2018-01-18 23:07:59 -0800
categories: Development
author: matt
excerpt: "Test Driven development is an evolutionary software programming approach, where you write tests before writing the functional code. TDD approach incorporate programming, testing and refactoring."
---
<!-- ### What is TDD? -->
![Article teaser]({{ "/assets/mocha-teaser.png" | absolute_url }})
*"Mocha.js",https://mochajs.org/, used under CC / Derivative of original*


**Test Driven development** is an evolutionary software programming approach, where you write tests before writing the functional code. TDD approach incorporate programming, testing and refactoring.
The usual steps taken while following TDD approach is :
- Individual test is written around a logic, which is meant to fail as no functional code exist.
- Test is run and will fail.
- Function is coded in such a way that it should pass the test.
- Test is again re-run to check the correct implementation of the function.
- If test passes, then function is refactored to improve quality and code maintainability.
- Test is again re-run to make sure function is still working after refactoring.


#### Advantages of using TDD:
- Reduced time at debugging stage.
- Improves modularity and code maintainability.
- Higher code coverage.

#### Disadvantages of using TDD:
- Requires god-level programming skills and patience.
- Requires to write clean and modular code.
- Tests are only as good as the person who is writing it.


### What is Mocha and chai?

[Mocha][mocha-docs] is a simple, extensible testing framework for [Node.js][node-docs]. It works with both, unit and integration testing. Mocha when used with TDD assertion library like [Chai][chai-docs], makes testing easy and expressive.
Chai assertion library provides helper function, to replace mammoth tasks of writing tests with few lines of code.


### Tutorial

In this tutorial we will go through steps for doing unit testing with mocha and chai on node.js.
The framework which i have used is Hapi.js. You can check its official document [here][hapi-docs].


- The most important thing is to install node.js on your local machine. You can find instruction of installation [here][node-install], as it depends on which operation system you are using.


- Once installed node.js on your machine, proceed with making a directory/folder named `my_project` or type in terminal to create a folder


    ```sh
mkdir my_project
```

- Open `my_project` folder in your favorite editor, i am using [Visual Studio Code][vscode-docs] for this tutorial.


- Add a file `index.js`, this is where all the server-side code goes.


    ![index file]({{ "/assets/index.png" | absolute_url }})


- Now open the terminal/command prompt, cd into project folder `my_project` and type following commands.(installing dependencies)


    ```sh
npm init  
#=> keep on pressing enter until u see ‘press y for yes option’,
#=> and then type yes to proceed
npm i -s hapi joi monk  
#=> these are the project dependencies
npm i –save-dev mocha chai inject-then 
#=> these are the developer dependencies
```
OR
    
    you can create a filename package.json inside `my_project` folder and type following content in it.
    After that, you have to run command `npm install` in terminal.
```sh
{
  "name": "my_project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "mocha"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "hapi": "^16.4.3",
    "joi": "^13.1.0",
    "monk": "^6.0.5"
  },
  "devDependencies": {
    "chai": "^4.1.2",
    "inject-then": "^2.0.9",
    "mocha": "^4.1.0"
  }
}
```


- Now your `package.json` file should look like this:


    ![package.json file]({{ "/assets/package.png" | absolute_url }})


- Once you are completed with installation of dependencies, we have to create a folder named `test` in the root of our project directory - means inside the `my_project` folder. In addition to folder, we also have to create a filename `test.js` inside the test folder. So, the final project structure looks like this:


    ```sh
my_project->
    index.js
    package.json
    test->
        test.js
```


- Inside the `test.js`, we will write the test using mocha and chai. These test will check whether our API is successfully running or not:

    
      const assert = require('chai').assert;
      const server = require('../index.js');
      const request = require('http');
      
      
      server.register([{
          register: require('inject-then')
      }])
      
      describe('Server Testing', function () {
          it('should validate if server is running', function () {
              return server.injectThen({
                      method: 'GET',
                      url: '/'
                  })
                  .then(
                      function (response) {
                          assert.deepEqual(response.statusCode, 200);
                      }
                  )
          })
          it('should invalidate if server is running', function () {
              return server.injectThen({
                      method: 'GET',
                      url: '/'
                  })
                  .then(
                      function (response) {
                          assert.notEqual(response.statusCode, 400);
                      }
                  )
          })
      })
    

- Once we are finished with creating our test for functional logic, we will run it dry and let it fail because currently their is no source code to test. In order to run mocha to start testing, we have to type `mocha` in the terminal.


    ![mocha command]({{ "/assets/mocha-type.png" | absolute_url }})


- I will take a pause and explain why we ran a test, before actually writing code it checks for. This is the real  scenario of test driven development, where you write test first and let it fail. In next iteration you write just enough code to make these tests pass. And once your testing are passing with flying colors, you can improve your functional code to increase it quality and maintainability and then run the test again to make sure everything is working fine after refactoring. 


- Now we will write the real function code, which our mocha test is going to check. You can copy below code in filename `index.js` - created in **Step 1**.

      const hapi = require( 'hapi');
      const server = new hapi.Server()
  
  
      server.connection({
          host:'localhost',
          port:Number(process.env.PORT || 8080 )
      })
  
      server.route({
          method:'GET',
          path:'/',
          handler: function (req,res){
              return res("Testing hapi with mocha and chai")
            }
      })
  
      server.start((err)=>{
        if (err) console.log('error while connecting :'+err)
      })
  
      module.exports = server;


- Aforementioned, we will re-run our mocha test again to check whether our code in `index.js` is working or not. So, type in terminal `mocha`


- As you can see that all tests are passing.


    ![mocha test pass]({{ "/assets/mocha-pass.png" | absolute_url }})


- At this step our API code is complete. Now we have to run the API, by typing `node index.js` in the terminal.


    ![api run command]({{ "/assets/node-index.png" | absolute_url }})


- Now switch to your browser and type following in the url:


    ```sh
http://localhost:8080/ 
```

- It will show 


    ![browser output]({{ "/assets/browser.png" | absolute_url }})


- So, we tested our API with mocha and chai; and ran it on local browser. Now,if we want to improve our code we can refactor it. Although our task is already completed.

#### Source Code
You can find source code of tutorial => **[here][gist]** 

#### References 

*"Introduction to Test Driven Development (TDD)." agiledata. Accessed January 18, 2018.
http://agiledata.org/essays/tdd.html*

*Rouse,Margaret. "test-driven development (TDD)." searchsoftwarequality.techtarget. May 2017. Accessed January 18,2018.
http://searchsoftwarequality.techtarget.com/definition/test-driven-development*


[mocha-docs]: https://mochajs.org/
[node-docs]: https://nodejs.org/en/
[chai-docs]: http://chaijs.com/
[hapi-docs]: https://hapijs.com/
[node-install]: https://nodejs.org/en/
[vscode-docs]: https://code.visualstudio.com/
[gist]: https://gist.github.com/codeAmar/adc1aaba5243b35a8ae3e11d4389e29c



