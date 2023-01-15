# this project has been taken in order to takes notes from.
# Node.js Backend Architecture Typescript Project

### A complete project to build a blogging platform like Medium, and FreeCodeCamp

errors that i faced:
  - must use TLS 1.2 or higher. You are currently using plaintext http to connect. Please visit the GitHub blog for more information: https://github.blog/2021-08-23-npm-registry-deprecating-tls-1-0-tls-1-1/{i fixed it be:
  ```git
  npm config set registry https://registry.npmjs.org/
  ```
## smart project assembly:
  - server.ts that contains the app.listen(port,()=>{Logger.info})
  - config.ts (has the db)

## mongoDB BASICS every one should knows:
   - process.on('SIGINT')
   - mongodb strictQuery property May be `false`, `true`, or `throw`, Sets the default [strictQuery] mode for schemas
   - mongoose.set, setting the mongoose options.
   - Mongoose plugins it is like a function that you can use in your schema and reuse again.
   - Using trim will help in removing the white spaces present (beginning and ending of the string) in the string that you want to save to the DB like.
   - model<EnforcedDocType>{DOCUMENT_NAME,schema,COLLECTION_NAME}
## mongoose methods and ways of using every one shoud knows:
  - Pre-save hooks in mongoose.js
  - Lean(), By default, Mongoose queries return an instance of the Mongoose Document class. Documents are much heavier than vanilla JavaScript objects, because they have a lot of internal state for change tracking. Enabling the lean option tells Mongoose to skip instantiating a full Mongoose document and just give you the POJO.
  
## basics i learned:
  - process.exit(1) exit with failure error, while the process.exit(0) exits without
  - The connection process in Mongoose inherits the Node EventEmitter class
  - Currently errors are first forwarded to the process.on('error') event before reaching process.on('uncaughtException')
  - emitter.on(event,listner)
  - extends keyof usage, public property names of T
  - Abstract classes are mainly for inheritance where other classes may derive from them.
  - CORS Configuration Options: origin(Configures the Access-Control-Allow-Origin CORS header), methods(Configures the Access-Control-Allow-Methods CORS header), allowedHeaders(ex: ['Content-Type', 'Authorization']), exposedHeaders, optionsSuccessStatus: a status code for successful OPTIONS requests, since some legacy browsers (IE11, various SmartTVs) choke on 204.
  - Process events({'beforeExit' makes asynchronous calls},)
  - Process.on(uncaughtException(uncaught exception bubbles to the event loop), unhandledRejection (The latter is used when an exception happens in a Promise based async context)
  - uncaughtException(uncaught exception bubbles to the event loop), unhandledRejection (The latter is used when an exception happens in a Promise based async context)
  - winston (one of the most popular logging library),(Use Structured Logging),post-mortem debugging
  - lib.es5{the most famous used interface is Error interface}
  - events.d.ts{global{var process=NODEJS.process;namespace NODEJS{interface Process extends EventEmitter}}}, process.d.ts{global{namespace NODEJS{EventEmitter{on}}}
  - inside the global redeclearing the namespace of NODEJS to declear new contents.
  - Symbol has no constructor function, (Symbols also work as property keys, but are not iterable, which is great for serialisation)
  - Global symbols registry, There’s a global symbols registry that allows you to access tokens across your whole application.
  - declearing global in typescript
  - Running tsc locally will compile the closest project defined by a tsconfig.json, you can compile a set of TypeScript files by passing in a glob of files you want.
  - (TLS) is a Transport Layer Security, it encrypts data sent over the Internet to ensure that eavesdroppers and hackers are unable to see what you transmit which is particularly useful for private and sensitive information such as passwords, credit card numbers, and personal correspondence.
  - winston is designed to be a simple and universal logging library with support for multiple transports, https://github.com/winstonjs/winston
  - baseUrl tells Typescript to base all your modules from a specific directory. Which means they will also be compiled with paths relative to baseUrl, but node is not aware of tsconfig.json, so it will try to find these modules in the path relative to the script and relative to NODE_PATH env variable, So the solution would be to pass NODE_PATH before running, like this:```git npx tsc && NODE_PATH=src node src/main/app.ts```
  -  

## joi library examples:
  - Joi.string().valid, .pattern(, .alphanum(), Joi.array().includes(Joi.string()), Joi.string().alphanum(), 
  - .pattern(/firstname|lastname/, Joi.string().min(2))
  - Joi.object().pattern(/\w{2,25}/, Joi.date().iso())


## functions i learned:
  
  - By calling Joi.object() you first create a schema that allows any keys and then by calling .keys([schema]) you extend that schema (basically the same as defining a new schema with Joi.object([schema]) )
  - new(): T, is a class type that is referred to as a constructor function. 
  - some mongodb functions: mongoose.connection.close, mongoose.connection.on, mongoose.plugin, mongoose.connect
  - res.append (Appends the specified value to the HTTP response header field.)
  - Object.assign(target, ...sources), it copies the values and properties from one or more source objects to a target object.
  - delete operator removes a property from an object.
  - array.join(separator)
  - toString() is an inbuilt function in TypeScript which is used to return a string representing the specified object.
  - indexOf() method returns the first index at which a given element can be found in the array, or -1 if it is not present.
## basic concepts every one should know it:
  - Joi is a library that lets us validate an object’s structure with ease.
  - They are generated on the project making the call, and you can restrict their use to an environment such as an IP address range, or an Android or iOS app. By identifying the calling project, you can use API keys to associate usage information with that project.
  - Sudo (superuser do) is a utility for UNIX- and Linux-based systems that provides an efficient way to give specific users permission to use specific system commands at the root (most powerful) level of the system. Sudo also logs all commands and arguments.
  - Multi-factor authentication is a layered approach to securing data and applications where a system requires a user to present a combination of two or more credentials to verify a user's identity for login
  - POJO stands for Plain Old Java Object. It is an ordinary Java object, not bound by any special restriction other than those forced by the Java Language Specification and not requiring any classpath. 
## some RegEx concepts:
  - i is used when it means that replace case-insensitive patterns

[![Docker Compose CI](https://github.com/janishar/nodejs-backend-architecture-typescript/actions/workflows/docker_compose.yml/badge.svg)](https://github.com/janishar/nodejs-backend-architecture-typescript/actions/workflows/docker_compose.yml)

Note: This is the **latest (version 2)** of the project. If you are using **version 1** then checkout the branch [**version-1**](https://github.com/janishar/nodejs-backend-architecture-typescript/tree/version-1)

<p align="center">
    <img src="https://raw.githubusercontent.com/janishar/nodejs-backend-architecture-typescript/master/addons/github_assets/cover-nodejs-backend.png">
</p>
<br>

# Project Highlights 
1. Node.js
2. Express.js
3. Typescript
4. Mongoose
5. Redis
6. Mongodb
7. Joi
8. Unit Tests & Integration Tests
9. Docker
10. JWT 

# About The Project
This project is designed for a production ready environment. It can handle the scale and complexity of a very demanding application. This project is being used by companies like MindOrks, AfterAcademy, and CuriousJr. Apps/Websites having 10+ million usebase.

It is suitable for Web Apps, Mobile Apps, and other API services.

# About The Author
I [Janishar Ali](https://janisharali.com) have created this project using my 10 years of experience in tech industry working for top companies. I enjoy sharing my learnings with the community. You can connect with me here:
* [Twitter](https://twitter.com/janisharali)
* [LinkedIn](https://www.linkedin.com/in/janishar-ali)
* [Instagram](https://www.instagram.com/janisharali)

[Learn from My YouTube Channel](https://www.youtube.com/@janisharali)

# Project Instructions
We will learn and build the backend application for a blogging platform. The main focus will be to create a maintainable and highly testable architecture.
<br>
Following are the features of this project:
* **This backend is written in Typescript**: The type safety at build time and having intellisense for it in the IDE like vscode is unparalleled to productivity. I have found production bug reduced to a significant amount since most of the code vulnerabilities are identified during the build phase itself.
* **Separation of concern principle**: Each component has been given a particular role. The role of the components is mutually exclusive. This makes the project easy to be unit tested.
* **Feature encapsulation**: The files or components that are related to a particular feature have been grouped unless those components are required in multiple features. This enhances the ability to share code across projects.
* **Centralised Error handling**: I have created a framework where all the errors are handled centrally. This reduces the ambiguity in the development when the project grows larger.
* **Centralised Response handling**: Similar to Error handling we have a response handling framework. This makes it very convenient to apply a common API response pattern.
* **Mongodb is used through Mongoose**: Mongodb fits very well to the node.js application. Being NoSQL, fast, and scalable makes it ideal for modern web applications.
* **Redis Memcache**: I have used the redis server for caching the items which does not change frequently. It will boost the performance of our system.
* **Async execution**: I have used async/await for the promises and made sure to use the non-blocking version of all the functions with few exceptions.
* **Docker compose has been configured**: I have created the Dockerfile to provide the easy deployability without any setup and configurations. 
* **Unit test is favored**: The tests have been written to test the functions and routes without the need of the database server. Integration tests has also been done but the unit test is favored.
* **A pure backend project**: I have experienced that when a backend is developed clubbed with a frontend then in the future it becomes really difficult to scale. We would want to create a separate backend project that servers many websites and mobile apps.

> I have also open source a complete blogging website working on this backend project: [Goto Repository](https://github.com/janishar/react-app-architecture)
The repository [**React.js Isomorphic Web Application Architecture**] has a complete React.js web application implemented for a blogging platform which is using this project as its API server.

## 3RE Architecture: Router, RouteHandler, ResponseHandler, ErrorHandler
<p align="center">
    <img src="https://raw.githubusercontent.com/janishar/nodejs-backend-architecture-typescript/master/addons/github_assets/3RE.png">
</p>
<br>

## Project Outline: Blogging Platform
<p align="center">
    <img src="https://raw.githubusercontent.com/janishar/nodejs-backend-architecture-typescript/master/addons/github_assets/project-outline.png">
</p>
<br>

## Request-Response Handling Schematic Diagram
<p align="center">
    <img src="https://raw.githubusercontent.com/janishar/nodejs-backend-architecture-typescript/master/addons/github_assets/api-structure.png">
</p>
<br>

## Learn the concepts used in this project
* [Design Node.js Backend Architecture like a Pro](https://janisharali.com/blog/design-node-js-backend-architecture-like-a-pro)
* [The video guide to build and run this project](https://youtu.be/t7blRxqPIMs)
* [Implement JSON Web Token (JWT) Authentication using AccessToken and RefreshToken](https://janisharali.com/blog/implement-json-web-token-jwt-authentication-using-access-token-and-refresh-token)
* [TypeScript Tutorial For Beginners](https://afteracademy.com/blog/typescript-tutorial-for-beginners)
* [From JavaScript to TypeScript](https://afteracademy.com/blog/from-javascript-to-typescript)


## You can find the complete API documentation [here](https://documenter.getpostman.com/view/1552895/2s8Z6u4a6N)
<a href="https://documenter.getpostman.com/view/1552895/2s8Z6u4a6N" target="_blank">
    <img src="https://raw.githubusercontent.com/afteracademy/nodejs-backend-architecture-typescript/master/addons/github_assets/api-doc-button.png" width="200" height="60"/>
</a>

## How to build and run this project

* Install using Docker Compose [**Recommended Method**] 
    * Clone this repo.
    * Make a copy of **.env.example** file to **.env**.
    * Make a copy of **keys/private.pem.example** file to **keys/private.pem**.
    * Make a copy of **keys/public.pem.example** file to **keys/public.pem**.
    * Make a copy of **tests/.env.test.example** file to **tests/.env.test**.
    * Install Docker and Docker Compose. [Find Instructions Here](https://docs.docker.com/install/).
    * Execute `docker-compose up -d` in terminal from the repo directory.
    * You will be able to access the api from http://localhost:3000
    * *If having any issue* then make sure 3000 port is not occupied else provide a different port in **.env** file.
    * *If having any issue* then make sure 27017 port is not occupied else provide a different port in **.env** file.
 * Run The Tests
    * Install node.js and npm on your local machine.
    * From the root of the project executes in terminal `npm install`.
    * *Use the latest version of node on the local machine if the build fails*.
    * To run the tests execute `npm test`.
 * Install Without Docker [**2nd Method**]
    * Install MongoDB on your local.
    * Do steps 1 to 5 as listed for **Install using Docker Compose**.
    * Do steps 1 to 3 as listed for **Run The Tests**.
    * Create users in MongoDB and seed the data taking reference from the **addons/init-mongo.js**
    * Change the `DB_HOST` to `localhost` in **.env** and **tests/.env.test** files.
    * Execute `npm start` and You will be able to access the API from http://localhost:3000
    * To run the tests execute `npm test`.

  * Postman APIs Here: 
    [addons/postman](https://github.com/janishar/nodejs-backend-architecture-typescript/tree/master/addons/postman)
  
  ## Learn Backend Development From Our Videos
  * [Introduction to Web Backend Development for Beginners](https://youtu.be/SikmqyFocKQ)
  * [Backend System Design for Startups](https://youtube.com/playlist?list=PLuppOTn4pNYeAn-ioA-Meec5I8pQK_gU5)
  * [Practical Javascript for Beginners](https://youtube.com/playlist?list=PLuppOTn4pNYdowBb05yG2I8wAmHiW7vze)
  
 ## Project Directory Structure
 ```
├── .vscode
│   ├── settings.json
│   ├── tasks.json
│   └── launch.json
├── .templates
├── src
│   ├── server.ts
│   ├── app.ts
│   ├── config.ts
│   ├── auth
│   │   ├── apikey.ts
│   │   ├── authUtils.ts
│   │   ├── authentication.ts
│   │   ├── authorization.ts
│   │   └── schema.ts
│   ├── core
│   │   ├── ApiError.ts
│   │   ├── ApiResponse.ts
│   │   ├── JWT.ts
│   │   ├── Logger.ts
│   │   └── utils.ts
│   ├── cache
│   │   ├── index.ts
│   │   ├── keys.ts
│   │   ├── query.ts
│   │   └── repository
│   │       ├── BlogCache.ts
│   │       └── BlogsCache.ts
│   ├── database
│   │   ├── index.ts
│   │   ├── model
│   │   │   ├── ApiKey.ts
│   │   │   ├── Blog.ts
│   │   │   ├── Keystore.ts
│   │   │   ├── Role.ts
│   │   │   └── User.ts
│   │   └── repository
│   │       ├── ApiKeyRepo.ts
│   │       ├── BlogRepo.ts
│   │       ├── KeystoreRepo.ts
│   │       ├── RoleRepo.ts
│   │       └── UserRepo.ts
│   ├── helpers
│   │   ├── asyncHandler.ts
│   │   ├── permission.ts
│   │   ├── role.ts
│   │   ├── security.ts
│   │   ├── utils.ts
│   │   └── validator.ts
│   ├── routes
│   │   ├── access
│   │   │   ├── credential.ts
│   │   │   ├── login.ts
│   │   │   ├── logout.ts
│   │   │   ├── schema.ts
│   │   │   ├── signup.ts
│   │   │   ├── token.ts
│   │   │   └── utils.ts
│   │   ├── blog
│   │   │   ├── editor.ts
│   │   │   ├── index.ts
│   │   │   ├── schema.ts
│   │   │   └── writer.ts
│   │   ├── blogs
│   │   │   ├── index.ts
│   │   │   └── schema.ts
│   │   ├── index.ts
│   │   └── profile
│   │       ├── schema.ts
│   │       └── user.ts
│   └── types
│       └── app-request.d.ts
├── tests
│   ├── auth
│   │   ├── apikey
│   │   │   ├── mock.ts
│   │   │   └── unit.test.ts
│   │   ├── authUtils
│   │   │   ├── mock.ts
│   │   │   └── unit.test.ts
│   │   ├── authentication
│   │   │   ├── mock.ts
│   │   │   └── unit.test.ts
│   │   └── authorization
│   │       ├── mock.ts
│   │       └── unit.test.ts
│   ├── core
│   │   └── jwt
│   │       ├── mock.ts
│   │       └── unit.test.ts
│   ├── cache
│   │   └── mock.ts
│   ├── database
│   │   └── mock.ts
│   ├── routes
│   │   ├── access
│   │   │   ├── login
│   │   │   │   ├── integration.test.ts
│   │   │   │   ├── mock.ts
│   │   │   │   └── unit.test.ts
│   │   │   └── signup
│   │   │       ├── mock.ts
│   │   │       └── unit.test.ts
│   │   └── blog
│   │       ├── index
│   │       │   ├── mock.ts
│   │       │   └── unit.test.ts
│   │       └── writer
│   │           ├── mock.ts
│   │           └── unit.test.ts
│   ├── .env.test
│   └── setup.ts
├── addons
│   └── init-mongo.js
├── keys
│   ├── private.pem
│   └── public.pem
├── .env
├── .gitignore
├── .dockerignore
├── .eslintrc
├── .eslintignore
├── .prettierrc
├── .prettierignore
├── .travis.yml
├── Dockerfile
├── docker-compose.yml
├── package-lock.json
├── package.json
├── jest.config.js
└── tsconfig.json
 ```
 
 ## Directory Traversal for Signup API call
 `/src → server.ts → app.ts → /routes/index.ts → /auth/apikey.ts → schema.ts → /helpers/validator.ts → asyncHandler.ts → /routes/access/signup.ts → schema.ts → /helpers/validator.ts → asyncHandler.ts → /database/repository/UserRepo.ts → /database/model/User.ts → /core/ApiResponses.ts`
 
 ## API Examples
* Signup
    * Method and Headers
    ```
    POST /signup/basic HTTP/1.1
    Host: localhost:3000
    x-api-key: GCMUDiuY5a7WvyUNt9n3QztToSHzK7Uj
    Content-Type: application/json
    ```
    * Request Body
    ```json
    {
        "name" : "Janishar Ali",
        "email": "ali@github.com",
        "password": "changeit",
        "profilePicUrl": "https://avatars1.githubusercontent.com/u/11065002?s=460&u=1e8e42bda7e6f579a2b216767b2ed986619bbf78&v=4"
    }
    ```
    * Response Body: 200
    ```json
    {
      "statusCode": "10000",
      "message": "Signup Successful",
      "data": {
        "user": {
          "_id": "63a19e5ba2730d1599d46c0b",
          "name": "Janishar Ali",
          "roles": [
             {
               "_id": "63a197b39e07f859826e6626",
               "code": "LEARNER",
               "status": true
             }
            ],
          "profilePicUrl": "https://avatars1.githubusercontent.com/u/11065002?s=460&u=1e8e42bda7e6f579a2b216767b2ed986619bbf78&v=4"
        },
        "tokens": {
          "accessToken": "some_token",
          "refreshToken": "some_token"
        }
      }
    }
    ```
    * Response Body: 400
    ```json
    {
      "statusCode": "10001",
      "message": "Bad Parameters"
    }
    ```
* Profile Private
    * Method and Headers
    ```
    GET /profile/my HTTP/1.1
    Host: localhost:3000
    x-api-key: GCMUDiuY5a7WvyUNt9n3QztToSHzK7Uj
    Content-Type: application/json
    Authorization: Bearer <your_token_received_from_signup_or_login>
    ```
    * Response Body: 200
    ```json
    {
      "statusCode": "10000",
      "message": "success",
      "data": {
        "name": "Janishar Ali Anwar",
        "profilePicUrl": "https://avatars1.githubusercontent.com/u/11065002?s=460&u=1e8e42bda7e6f579a2b216767b2ed986619bbf78&v=4",
        "roles": [
          {
            "_id": "5e7b8acad7aded2407e078d7",
            "code": "LEARNER"
          },
          {
            "_id": "5e7b8c22d347fc2407c564a6",
            "code": "WRITER"
          },
          {
            "_id": "5e7b8c2ad347fc2407c564a7",
            "code": "EDITOR"
          }
        ]
      }
    }
    ```

### Find this project useful ? :heart:
* Support it by clicking the :star: button on the upper right of this page. :v:

### License
```
   Copyright (C) 2022 JANISHAR ALI ANWAR

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
```
     
 
