# this project has been taken in order to takes notes from.
# Node.js Backend Architecture Typescript Project

### A complete project to build a blogging platform like Medium, and FreeCodeCamp

errors that i faced:
  - must use TLS 1.2 or higher. You are currently using plaintext http to connect. Please visit the GitHub blog for more information: https://github.blog/2021-08-23-npm-registry-deprecating-tls-1-0-tls-1-1/{i fixed it be:
  ```git
  npm config set registry https://registry.npmjs.org/
  ```

DB Schema:
//NB: RoleCode(LEARNER,WRITER,EDITOR,ADMIN)||_id:Types.ObjectId
ApiKey(_id,key,version,permissions,comments,status?,createdAt,updatedAt)
RoleModel(code:s-enum,status:b,createdAt,updatedAt)
User(_id,name?,profilePicUrl?,email?,password?,roles[],verified?:b, status?,createdAt?,updatedAt?)
Keystore(_id,client,primaryKey,secondaryKey,status?,createdAt?,updatedAt?)
Blog(_id,title,description,text?,draftText?,tags,author,imgUrl?,blogUrl,likes?: n,score: n,isSubmitted: b,isDraft: b,isPublished: b,status?: b,publishedAt?,createdBy?: User,updatedBy?: User, createdAt?,updatedAt?)
## for res we have.
  - .send
  - .render
  - .json





## Notes before the project assembly:
  - 
  - the validate by many way, but acually it depends on how accurate we want to validate, for example, we validate the api-key with joi to make an accurate validation, but we validated the payloud samply by (!payload ||!payload.iss ...etc)
  - for validation purposes, for one middleware, we can set the type of the req to make sure that it contains a certain content like, like req: PublicRequest
  - we use the authentication in the three of:(credential|profile|logout(post(L,validator,))), by .use before the middleware method(like .post)
  - 




## project assembly:
  - the permission is in the req.apikey, the apikey was put in the req.headers, password in the req.body
  - the data base starts form 
  - use routes with path of the '/' `express.Router()`
  - classifying the routes by `router.use` for each route like '/signup' including the apikey and  permission
  - apikey is the route also `express.Router` the use many args of (validator,asyncHandler)
  - the validator takes the two args of schema.apiKey `Joi.object().keys({x-api-key: Joi.string().required(), }) .unknown(true),` and 'headers' string as the `ValidationSource.HEADER`
  - the validator starts with `const{error}=schema.validate(req['headers'])` then ` {details}=error` then `message = details.map((i) => i.message.replace(/['"]+/g,'')).join(',');`
  - schema has the the apiKey and the auth which is `Joi.object() .keys({ authorization: JoiAuthBearer().required(),}).unknown(true),`
  - JoiAuthBearer is `Joi.string().custom(`
  - the apikey asyncHandler takes inside it an async function (req:PublicRequest extends Request{apiKey:ApiKey;},res,next) which will get the key_p as req.headers['x-api-key'] and `key = req.headers[Header.API_KEY]?.toString();` or throw new error. , the next step
  - finding the apiKey from the mongoose by awaiting a function of findByKey
  - findByKey is a function that takes one arg and return a Promise<ApiKey | null | undefined> inside this function it retruns `ApiKeyModel.findOne{ key: key, status: true }` , setting `req.apiKey = apiKey;` , not to forgeting to return `next()`
  - the permission is an arrow function that after giving it the first arg of `'GENERAL'` as permission, the function will arrows to reqhandler func tha take (req,res,next) inside the reqhandler we have the try which inside it if `(!req.apikey!.permission)` return next(new ForbiddenError), then `exists= req.apikey.permission.find((entry)=>entry=== `'GENERAL'` as permission)`
  - we can add an extra permission beside the `GENERAL = 'GENERAL'`
  - signup middleware starts with two args(validator, async function) with the validator that does: the same as the last explained validator
  - the signup asyncHandler takes inside it an async function (req:RoleRequest extends Request{currentRoleCodes:string[];},res,next) which will get search as `UserRepo.findByEmail(req.body.email)` if exist then throw an error.
  - then awaiting the async create function:Promise<{ user: User; keystore: Keystore }> which takes four parameters{user:User,accessTokenKey:string,refreshTokenKey:string, roleCode:s('LEARNER')}, the accessTokenKey and refreshTokenKey are created by `crypto.randomBytes(64).toString('hex');` and the refreshTokenKey
  - //the User interface contains (_id:Types.ObjectId,name?,profilePicUrl?,email?,password?,roles:Role[],verified?:boolean,status?:boolean;createdAt?:Date,updatedAt?:Date)
  - //the Role interface contains (_id:Types.ObjectId,code:s,status?:boolean,createdAt?,updatedAt?)
  - //the Keystore interface contains( _id:Types.ObjectId,client:User,primaryKey,secondaryKey,status?:b;createdAt?,updatedAt?)
  - the create function does RoleModel.**findOne({code**:'LEARNER'}).select('+code') if (!) throw E, then it **user.roles**=[role];**user.createdAt**=user.updatedAt=now, then createdUser=await**UserModel.create(user)**,keystore =await **KeystoreRepo.create**(createdUser, accessTokenKey, refreshTokenKey, );
  - then keystore=**await UserModel.create(user,primaryKey,secondaryKey)**:Promise<Keystore> that will do **KeystoreModel.create**(User,primaryKey,secondaryKey) and return `keystore.toObject()`
  - in brief the signup{-findByEmail/error 2-accessTokenKey/refreshTokenKey 3-passwordHash 4-UserRepo.create(name: req.body.name, etc)4- inside the UserRepo.create role=RoleModel.findOne, user.roles=[role];createdUser=await UserModel.create(user); const keystore=await KeystoreRepo.create 5-tokens=await createTokens 6- userData=await getUserData{_.pick(user, ['_id', 'name', 'roles', 'profilePicUrl'])} 7-
  - createTokens:Promise<Tokens> will sign the payloud(user_data) with the private Key,
  - we made a JwtPayload class with(aud,sub,iss,prm,iat=Math.floor(Date.now() / 1000),exp=iat+validity)
  - in brief we gave the payloud to the jwt.sign as {tokenInfo.issuer,tokenInfo.audience,user._id.toString(),accessTokenKey,tokenInfo.accessTokenValidity}
  - we created the accessTokenKey by hex before in
  - at the end of the signup file we used the getUserData in order to retrieve the data directly from the DB so we make sure that all the steps went fine.
  - authentication:(validate auth:header,getToken, JWT.validate, validateTokenpayloud, setting user.user req.keystore)
  - authentication:1-{va_schema.auth(req[header]authorization: JoiAuthBearer(),header)} | 2-getAccessToken(req.headers.authorization){no/or no barear-->error Barear Token} | 3-payload = await JWT.validate | 4-validateTokenData{||!payload.prm||payload.aud!==tokenInfo.audience||!Types.ObjectId.isValid(payload.sub)} | 5-UserRepo.findById (req.user = user)--> KeystoreRepo.findforKey(req.keystore = keystore)
  - role:1- req.currentRoleCodes = roleCodes;
  - authorization: 1-(!req.user || !req.user.roles || !req.currentRoleCodes) | 2-findByCodes(req.currentRoleCodes) | 3-for(const userRole of req.user.roles): authorized = true;break;next or error

  
  
  
  


## fast programming shortcuts:
  - when taking an arg and its type you can use the arg a type since you decleared its type.
  - `const data = header['apikey']?.tostring()`
  
  
## smart project assembly:
  - server.ts that contains the app.listen(port,()=>{Logger.info})
  - config.ts (has the db)


## mongoose database assembly declearation:
  - creating the database modeles 

## mongoDB BASICS every one should knows:
(https://mongoosejs.com/docs/3.2.x/docs/guide.html#versionKey)
  
  - MongoDB ObjectId: MongoDB creates a unique 12 bytes ID for every object using the timestamp of respective Object creation. This ObjectId can be used to uniquely target a specific object in the database. Structure: 4-byte timestamp value 5-byte random value 3-byte incrementing counter, initialized to a random value(that is why we use (ObjectId.isValid(id),However, it ObjectId.isValid(id) returns true even for invalid strings with length 12.)
  - we have to use either .toObect() when getting the result of the query, or to use, because mongoose functions don't represent actual JavaScript objects. To get an actual JavaScript object you have to call (oddly enough) toObject().
  - This allow your application code to test if changes have been made between a fetch (bringing in version key 42 for example) and a consequent update (ensuring version value still is 42). If version key has a different value (eg. 43 because an update has been made to the document), your application code can handle the concurrent modification.(https://stackoverflow.com/questions/17810637/mongoose-versioning-when-is-it-safe-to-disable-it)
  - Document versioning can also be disabled by setting the versionKey to false. DO NOT disable versioning unless you know what you are doing.
  - When versionKey is enabled, the version value is atomically incremented whenever a document is updated.
  - The __v field is called the version key. It describes the internal revision of a document. This __v field is used to track the revisions of a document. By default, its value is zero. In real practice, the __v field increments by one only when an array is updated. In other situations, the value of the __v field remains unaffected. So to keep the track of __v field in such situations, we can do it manually using the increment operator provided by the mongoose.
  - Partial indexes only index the documents in a collection that meet a specified filter expression. By indexing a subset of the documents in a collection, partial indexes have lower storage requirements and reduced performance costs for index creation and maintenance.
  - for the _id the index is normaly created by default.
  - the consequence of using alot of indexes: index creation can cause a significant performance impact. Disable the behavior by setting the autoIndex option of your schema to false, or globally on the connection by setting the option autoIndex to false.
   Data in MongoDB is stored in JSON format. More precisely said, data is stored in BSON format. BSON format provides a variety of data types. We can insert data from mongo shell or through mongoose. In both cases, the _id field is generated automatically in each document. The _id field acts as a primary key. It is unique throughout the collection. But there is one difference when inserting document(s) through mongoose. It auto-generates one more field. This field is the __v field. In this article, we will discuss what this __v field is and when it gets created.
   - Population is the process of automatically replacing the specified paths in the document with document(s) from other collection(s). We may populate a single document, multiple documents, plain object, multiple plain objects, or all objects returned from a query. Let's look at some examples.
   for example, Story .find(...) .populate({ path: 'fans', match: { age: { $gte: 21 }}, select: 'name -_id', options: { limit: 5 }
   path is the name of the collection, match the specified condition, the results will be showed accourdin to, options here we said we only want 5 results to be showed.
   (https://www.grepper.com/tpc/mongoose+populate%20/%20......http://man.hubwiz.com/docset/Mongoose.docset/Contents/Resources/Documents/mongoosejs.com/docs/populate.html)
   - array of ObjectIds
   - mongoose methods and statics, and its declearing.
   - process.on('SIGINT')
   - mongodb strictQuery property May be `false`, `true`, or `throw`, Sets the default [strictQuery] mode for schemas
   - mongoose.set, setting the mongoose options.
   - Mongoose plugins it is like a function that you can use in your schema and reuse again.
   - Using trim will help in removing the white spaces present (beginning and ending of the string) in the string that you want to save to the DB like.
   - model<EnforcedDocType>{DOCUMENT_NAME,schema,COLLECTION_NAME}
## mongoose methods and ways of using every one shoud knows:
  -  .lean() converts mongoose.Document to Plain Javascript Object (for create you must use .Object() because it returns a document)
  - Pre-save hooks in mongoose.js
  - Lean(), By default, Mongoose queries return an instance of the Mongoose Document class. Documents are much heavier than vanilla JavaScript objects, because they have a lot of internal state for change tracking. Enabling the lean option tells Mongoose to skip instantiating a full Mongoose document and just give you the POJO.
  - select:false, Currently, if we use select: false in a schema, we need to do Model.findOne({ _id }).select("+field_a") to get its value.
  
## basics i learned:
  - We used private.key to sign JWT and public.key to verify it
  - modal.create, Creates a new document or documents.
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
  - var is function scoped(available only within that function.,If outside of it, then available everywhere i.e. a global variable.,you can refer to it anywhere inside the function in which it is defined.)
  - let & const are block-scoped. They are scoped to the block in which they are declared i.e. inside the if/try/catch/while/for or any code block.
  - let & const If outside the function and code block, then they are available globally or become a global variable.
## joi library examples:

  - Joi.string().valid, .pattern(, .alphanum(), Joi.array().includes(Joi.string()), Joi.string().alphanum(), 
  - .empty(), it allows the empty string and treat it the same as undefined (i.e., convert it to undefined)
  - .pattern(/firstname|lastname/, Joi.string().min(2))
  - Joi.object().pattern(/\w{2,25}/, Joi.date().iso())
  - .allow, for examples allowing the null or ''.
  - .custom(fn: CustomValidator) ,type CustomValidator<V=any>=(value: V,helpers:CustomHelpers) =>V
  
## functions i learned:
  - RequestHandler Type is (req: Request, res: Response, next: NextFunction)=>void
  - Asyncfunction Type is (req: Request, res: Response, next: NextFunction)=>promise {so you can do the catch then}
  if you need to use the .catch you have to decleare the type of the AsyncFunction
  - .explain("executionStats")
  - .toString('hex'), it will just join the values into a comma-separated list of the values in decimal representation.
  - bcrypt.hash(string,number: The number of rounds to secure the hash. The number commonly ranges from 5 to 15,The callback function to execute when finishing the hashing)
  - The crypto.randomBytes() method is used to generate a cryptographically well-built artificial random data and the number of bytes to be generated in the written code.
  - .split([separator][, limit]); returns the new array. Also, when the string is empty, split returns an array containing one empty string, rather than an empty array. for ex, str.split(" ", 3); 
  - By calling Joi.object() you first create a schema that allows any keys and then by calling .keys([schema]) you extend that schema (basically the same as defining a new schema with Joi.object([schema]) )
  - new(): T, is a class type that is referred to as a constructor function. 
  - some mongodb functions: mongoose.connection.close, mongoose.connection.on, mongoose.plugin, mongoose.connect
  - res.append (Appends the specified value to the HTTP response header field.)
  - Object.assign(target, ...sources), it copies the values and properties from one or more source objects to a target object.
  - delete operator removes a property from an object.
  - array.join(separator)
  - toString() is an inbuilt function in TypeScript which is used to return a string representing the specified object.
  - indexOf() method returns the first index at which a given element can be found in the array, or -1 if it is not present.
  - startsWith() method determines whether a string begins with the characters of a specified string, returning true or false as appropriate.
  - The path.join() method joins the specified path segments into one path.
  - __dirname is an environment variable that tells you the absolute path of the directory containing the currently executing file.
  - The util.promisify() method basically takes a function as an input that follows the common Node.js callback style, i.e., with a (err, value) and returns a version of the same that returns a promise instead of a callback.
  - The fs.readFile() method is an inbuilt method which is used to read the file. This method read the entire file into buffer. To load the fs module we use require() method. For example: var fs = require(‘fs’);
  fs.readFile( filename, encoding, callback_function ) default value of encoding is ‘utf8’,callback_function(err,data: Contents of the file)
  - The parseInt method parses a value as a string and returns the first integer. , 2 = binary, 8 = octal, 10 = decimal, 16 = hexadecimal. , If radix is omitted, JavaScript assumes radix 10 (some times it is used to remove the point and the numbers after, and also to remove the spaces) we used it for the token validate time.
  - Math.floor() static method always rounds down and returns the largest integer less than or equal to a given number.
  - ObjectId.isValid(id){However, it ObjectId.isValid(id) returns true even for invalid strings with length 12.}
  - length: length is a final variable applicable for arrays. With the help of the length variable, we can obtain the size of the array.
  - from the BSON library .equals(Compares the equality of this ObjectId with `otherID`.
  - from the lodash library .pick(user, ['_id', 'name', 'email']){Creates an object composed of the picked `object` properties.}
  
  
  
  
  
  
  
  

## basic concepts every one should know it:
  
  - .PEM, Privacy Enhanced Mail (PEM) files are a type of Public Key Infrastructure (PKI) file used for keys and certificates. PEM, initially invented to make e-mail secure, is now an Internet security standard.
  - A relational database management system (RDBMS) is a program used to create, update, and manage relational databases. Some of the most well-known RDBMSs include MySQL, PostgreSQL, MariaDB, Microsoft SQL Server, and Oracle Database.
  - Joi is a library that lets us validate an object’s structure with ease.
  - They are generated on the project making the call, and you can restrict their use to an environment such as an IP address range, or an Android or iOS app. By identifying the calling project, you can use API keys to associate usage information with that project.
  - Sudo (superuser do) is a utility for UNIX- and Linux-based systems that provides an efficient way to give specific users permission to use specific system commands at the root (most powerful) level of the system. Sudo also logs all commands and arguments.
  - Multi-factor authentication is a layered approach to securing data and applications where a system requires a user to present a combination of two or more credentials to verify a user's identity for login
  - POJO stands for Plain Old Java Object. It is an ordinary Java object, not bound by any special restriction other than those forced by the Java Language Specification and not requiring any classpath. 
  - SSL stands for Secure Sockets Layer, It uses encryption algorithms to scramble data in transit, preventing hackers from reading it as it is sent over the connection. This information could be anything sensitive or personal which can include credit card numbers and other financial information, names and addresses.
  - regarding SSL: TLS (Transport Layer Security) is just an updated, more secure, version of SSL
  - regarding SSL: HTTPS (Hyper Text Transfer Protocol Secure) appears in the URL when a website is secured by an SSL certificate. The details of the certificate, including the issuing authority and the corporate name of the website owner, can be viewed by clicking on the lock symbol on the browser bar.
  - hexadecimal? Hexadecimal is a numbering system with base 16
  - Authentication is the process of determining whether someone or something is, in fact, who or what it says it is. Authentication technology provides access control for systems by checking to see if a user's credentials match the credentials in a database of authorized users or in a data authentication server.
  - A credential is a data structure that provides proof of an application's claim to a principal name. An application uses a credential to establish its global identity.
  - A user credential is a user name and password authentication token that is bound to a particular user. You can create user credentials for a user.
  - Authorization is the process where the database manager gets information about the authenticated user. Part of that information is determining which database operations the user can perform and which data objects a user can access.
  - authentication in the logout, you definitely must authenticate the /logout endpoint, to prevent an attacker from forcefully logging out all your users. If you do not validate this endpoint, anyone can logout any user. Hence this endpoint must be protected.
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
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
     
 
