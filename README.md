This project has been taken in order to take notes.
# Node.js Backend Architecture Typescript Project

### A complete project to build a blogging platform like Medium, and FreeCodeCamp

errors that I faced:
  - must use TLS 1.2 or higher. You are currently using plaintext http to connect. Please visit the GitHub blog for more information: https://github.blog/2021-08-23-npm-registry-deprecating-tls-1-0-tls-1-1/{i fixed it be:
  ```git
  npm config set registry https://registry.npmjs.org/
  ```

DB Schema:
//NB: RoleCode(LEARNER,WRITER,EDITOR,ADMIN)||_id:Types.ObjectId
- interface Error: name, message, stack?
-ApiKey(_id,key,version,permissions,comments,status?,createdAt,updatedAt)
-RoleModel(code:s-enum,status:b,createdAt,updatedAt)
-User(_id,name?,profilePicUrl?,email?,password?,roles[],verified?:b, status?,createdAt?,updatedAt?)
-Keystore(_id,client,primaryKey,secondaryKey,status?,createdAt?,updatedAt?)
-Blog(_id,title,description,text?,draftText?,tags,author,imgUrl?,blogUrl,likes?: n,score: n,isSubmitted: b,isDraft: b,isPublished: b,status?: -b,publishedAt?,createdBy?: User,updatedBy?: User, createdAt?,updatedAt?)
-interface Tokens{accessToken,refreshToken}
-interface JwtPayload(issuer: s, audience:s, subject: s,param:s,validity: number){sub:user id, param:the key in the keystore}

## pm2 (monitoring the process in the production):
- first npm install pm2 -g, if it is not recognized try to install it without global npm install pm2.
- pm2 start index.js ||||| pm2 list ||||| pm2 monit ||||| pm2 logs ||||| 
- 


## customizing VS Code:
- Cobalt2, server live, setting (format on save), setting (Font Family-->('cascadia code')), setting (Font Family-->(font ligatures))
- eslint npm install eslint
## project debugging:
- first building the ts files is so important, it can be done by npx tsc.
- running the file normally without debugging by node .\dist\index.js
- by debugging, node --inspect .\dist\index.js  -===then>>>>chrome //inspect/#devices
- debugging without the need to go to the chrome and staying with the same cli, node inspect .\dist\index.js
- in general you build with npx tsc && then you run the node --inspect or node inspect.
- to do it in one command, you do the (node --inspect -r ts-node/register ./src/demo.ts)

## for res we have.
  - .send
  - .render
  - .json


## nodejs redis:
  - Redis is a super fast and efficient in-memory, key–value cache and store. It’s also known as a data structure server, as the keys can contain strings, lists, sets, hashes and other data structures.
  - Redis is an open-source, in-memory data structure store used as a database, cache, and message broker. It has multiple uses, like caching NodeJS applications and API responses for faster performance.

You can think of it as a No-SQL database, which stores data as a key-value pair in the system memory. Redis supports disk-persistent data storage, too, if needed.

Redis supports storing multiple data structures and types, including strings, lists, hashes, sets, and sorted sets. Supported data structures give Redis versatility for many use cases.
  - If you don’t already have a Redis instance, you have a few options to get one:
  1. Run a free Redis instance from redis.io
  2. Install Redis locally, here’s how.
  3. Run Redis on Linodo (cheap but not free)
  4. Run Redis with Docker (my default for development){docker run -d --name redis-stack-server -p 6379:6379 redis/redis-stack-server:latest}
  - To read data from a Redis Database you can use the method get from the Redis Client. For example: redisClient.get('MY_CACHE_KEY');
  - npm install express axios redis.
  - const redisClient= redis.createClient();
  - redisClient.set('MY_CACHE_KEY', 'VALUE');, redisClient.get('MY_CACHE_KEY')
  - (async () =>{await client.connect();})();
  - redisClient.on('SIGINT',async ()=>{client.disconnect()});
  - 
## Doing redis keys for the Blogs:
  - first setting the enum DynamicKey with the two keys of BLOGS_SIMILAR = 'BLOGS_SIMILAR', BLOG='BLOG'
  - export type DynamicKeyType = ``${DynamicKey}_${string}``
  - export function getDynamicKey(Key:DynamicKey,suffix:string){}
  - ex fun getdyn(dkey,string){const D:DKE=DKEY_S}
  
## Doing redis query:
  - ex async function keyExists(...keys){return (await cache.exists(keys)) ? true : false;}
  - 



## function findIpAddress(req:Request) :
  - if (req.headers['x-forwarded-for']) { return req.headers['x-forwarded-for'].toString().split(',')[0]
  - 

## Notes before the project assembly:
  - we use the validate(schema.auth,HEADER) when you want to get to the req.header
  - the validate by many ways, but actually it depends on how accurately we want to validate, for example, we validate the api-key with joi to make an accurate validation, but we validated the payload simply by (!payload ||!payload.iss ...etc)
  - for validation purposes, for one middleware, we can set the type of the req to make sure that it contains a certain content like, like req: PublicRequest
  - we use the authentication in the three of:(credential|profile|logout(post(L,validator,))), by .use before the middleware method(like .post)
  - the authentication: in brief gives the req.user and req.keystore from the Jwt.validate(Token)
  - the authorization:(!req.user || !req.user.roles || !req.currentRoleCodes)-->findByCodes(req.currentRoleCodes)-->for(constof req.user.roles)-->authorized 
  - the credential(changing the password of the admin) after auth,rolegetting,authoriz,1-validate_credential(email,pass),2- finduserbyemail,3-passhash,4-UserRepo.update,5- keystoreRepo.RemoveAll, 6-forClient success response
  - The post refrToken:1- validate the header and body's Token, 2-
  - authorization( authorized = true -->next() or Permission denied)
  - authentication(req.user=, req.keystore= )
  - Role(req.currentRoleCodes = roleCodes)
  - credential(user/assign){changing password}: (authentication,Role,authorization)val(ema,pass)UserRepo.updateInfo()

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
  - findByKey is a function that takes one arg and return a Promise<ApiKey | null | undefined> inside this function it returns `ApiKeyModel.findOne{ key: key, status: true }` , setting `req.apiKey = apiKey;` , not to forgetting to return `next()`
  - the permission is an arrow function that after giving it the first arg of `'GENERAL'` as permission, the function will arrows to reqhandler func tha take (req,res,next) inside the reqhandler we have the try which inside it if `(!req.apikey!.permission)` return next(new ForbiddenError), then `exists= req.apikey.permission.find((entry)=>entry=== `'GENERAL'` as permission)`
  - we can add extra permission beside the `GENERAL = 'GENERAL'`
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
  - in brief, we gave the payload to the jwt.sign as {tokenInfo.issuer,tokenInfo.audience,user._id.toString(),accessTokenKey,tokenInfo.accessTokenValidity}
  - we created the accessTokenKey by hex before in
  - at the end of the signup file we used the getUserData in order to retrieve the data directly from the DB so we make sure that all the steps went fine.
  - authentication:(validate auth:header,getToken, JWT.validate, validateTokenpayloud, setting user.user req.keystore)
  - * authentication:1-{va_schema JoiAuthBearer(),header)} | 2-getAccessToken(req.headers.authorization){no/or no barear-->error Barear Token} | 3-payload = await JWT.validate | 4-validateTokenData{||!payload.prm||payload.aud!==tokenInfo.audience||!Types.ObjectId.isValid(payload.sub)} | 5-UserRepo.findById (req.user = user)--> KeystoreRepo.findforKey(req.keystore = keystore)
  - * role:1- req.currentRoleCodes = roleCodes;
  - authorization: 1-(!req.user || !req.user.roles || !req.currentRoleCodes) | 2-roles=findByCodes(req.currentRoleCodes) | 3-for(const userRole of req.user.roles):for(of roles) authorized = true;break;next or error
  - the credential starts with schema.validate(body){email,password}
  - UserRepo.findByEmail(req.body.email)-->!user then error -->
  - * RefreshToken:
1-val(auth, refresh Token)2- JWT.decode(req.accessToken)
3-validateTokenData 4-UserRepo.findById 5-JWT.validate(req.body.refreshToken) 6-validateRefrTokenData7-keystore.find 8-keystore.rmove9-accesstoken, refreshtoken=hex 10-KeystoreRepo.create 11-tokens = createTokens 12-new TokenRefreshResponse(the two Tokens).send(res);
  
  
  
  
##notes before going to the class of ApiResponse:
   - private - Only the current class will have access to the field or method.
   - protected - Only the current class and subclasses (and sometimes also same-package classes) of this class will have access to the field or method.
   - Protected: This keyword allows a little more access than private members but a lot less than the public. A protected member is accessible within the class (similar to private) and any object that inherits from it. A protected value is shared across all layers of the prototype chain. It is not accessible by anybody else.

   - public - Any class can refer to the field or call the method.
   - static, you can call static methods, and access static properties, without having to instantiate class in which they are defined. Second, you can’t call these methods, and access these properties, on instances of the class in which they are defined. 
   - public, All methods defined in a class are by default defined as public. This means that they will be accessible for all instances. Which also means that you can call them in all instances. However, you can’t call them on the class in which they are defined unless you instantiate it.
  - private static, private: used only inside the same class, static: it's implemented by adding. point of the father declared class.
  - protected members or methods can be used inside the class that implements the father class, in this case, it is the ApiResponse.
  - when we said protected prepare, we need: it means that we will use inside the main class of ApiResponse and inside the class that will extend the ApiResponsem By using super. prepare, and we will give it the class of TokenRefreshResponse that the super. prepare is used in as a type, so we are giving the container class as a type.
  - the public method is implemented by the object, while the static method is implemented by the father decleared class (ApiResponse)
  - when the abstract class extends an interface like Error, so when you do the super, you do not need to put all the interface properties inside the super.
  
## abstract class APIResponse:
  - 3* protected class members(statusCode,status,message) and:
  
  - 1* protected prepare<T extends ApiResponse>( res: Response, response: T, headers: { [key: string]: string }, ): Response
  - 1* public send( res: Response, headers: { [key: string]: string } = {}, ): Response, 
  
  - 1* private static sanitize<T extends ApiResponse>(response: T): T
  -we have the class.public send that will take a message from the usage area, and success codes from enum,
constructor(mes,private ATok,private RTok)super(statuscode,ResStatus,mes)
inside redefining public send protected prepare<this>(res,this)
inside the protected prepare<ex this>(res,response,headers)
return res.status(this.status).json(private static ApiResponse.sanitize(response))
inside the private static sanitize<ext this>(response){
const clone:T ={} as T;
for(const i in clone) if (typeof clone[i]==='undefined') delete clone[i];}
  -for the  Object.assign(clone, response); we put protectors for the members.
  
  
## abstract class ApiError extends interface Error:
  - interface Error:name,message,stack?
  - constructor all public(type,mes){super(type)}
  - we will use it in switch this is a reason but not a big reason for using the public for the members.
  - abstract class ApiError extends Error{
constructor all public(type,mes){super(type)}}
public static handle(err:ApiError,res:Response):Response{
  switch (err.type) {
    return new AuthFailureResponse(err.message).send(res)
    AuthFailureResponse{constructor(mes){super(protect code,status,mes)}}
    prepare<this>(res,this,header)
}
  -
## Docker instructions:

  RUN npm install --only=production(no installing devDependencies)
  

build image (docker build -t node-app-image) {dockerignore-->build again}
docker-compose up -d (build img and run cont accourding  to docker compose.yml)
docker-compose contrem + valrem(down -v) (up -d --build), .yml(-f basefile) (-f docker-compose.yml -f docker-compose.dev.yml -d) rebuilding(-f docker-compose.yml -f docker-compose.dev.yml -d --build)
  

cont run init(docker run -d --name node-app node-app-image)
cont exp (docker exec -it node-app)
cont to be used (running: docker ps,runornot: docker ps -a, docker run -v locmachC:\:fpathoncont/app  -p n:n -d --name x ima_name, docker run -v f:f -v preventedpath/app/node_modules --env PORT=4000 --env-file ./.env -p n:n -d --name im_name )
cont killing (docker rm node-app -f -fv,) /-f for force/
cont logs (docker logs node-app)
b:/app# (ls, cd,  )
app# print (cat index.js)
app# back to main cli(exit)
app# createfile (touch testfile)

  
  docker commands:
  - docker image ls(list the image that was created)
  - docker image rm(remove the image that was created), we can build with the same name, so overwriting.
  - docker build -t node-app-image . (`-t` for giving the image a name)(creating the image)
  - docker run -d --name node-app node-app-image(specify the image that it will create a container from)(naming the docker container by the name of node-app)(`-d`, by default when creating the container it will connected to cli of the container but by adding -d then it is the deattached mode, so our command line will be free and open)
  - docker ps(we see what is opened from containers)
  - docker rm node-app -f (remove the docker container or killing it)(`-f` for force)
  - docker ps(after killing all the containers we will see an empty list, which is the still running containers)
  - docker ps -a (show all containers started or stopped)
  - docker run -p outside traffic:container port 
  - docker run -p 3000:3000 -d --name node-app node-app-image(the left number it is the trafic coming in from the outside world, from the windows and then it will give it to the port of the right number which is the port of the container)(the right number is the port number that the container is listening to inside the conainer) 
  - docker exec -it node-app (opening file explorer in the docker container)(`-it` for interactive mode)(bash to allow us to look at the file system of our container)-->b:/app# 
  - b:/app# ls, if we input ls, we will see all the files inside the docker container 
  - b:/app# cd node_modules --> b:/app#/node_modules ls --> see all the modules
  - inside the explorer of 50c80624:/app#, we pass `ls` to list all the files that were copied over
  - docker rm node-app -f, we remove the container.
  - after setting the dockerignore file we will build again to see `docker build -t node-app-image .` 
  - after building our image, we run the container from the newly created image.
  - inside the explorer of 50c80624:/app#, we pass `cat index.js`, it will print the containt code of the mentioned index.js file  
  - docker run -v pathtofolderonlocamachine:pathtofolderoncontainer -p 3000:3000 -d --name node-app node-app-image(`-v` volume certain file from the container)(every change will be transfered to the container) but we need to restart the node process.
  - pathtofolderonlocamachine cannot be .,% , for windows powershall we use $(pwd),or it must be like C:\ etc.
  - pathtofolderoncontainer /app 
  - inside the explorer of 50c80624:/app#, we pass `exit`, to get back to the main cli.
  - we added nodemon to the package.json dependencies and the npm run command of "dev":nodemon -L index.js, then we built the image, then we run the container
  - so then we change the CMD to ["npm", "run", "dev"]
  - docker ps -a (we see all the containers started or stopped)
  - docker logs node-app (show the logs of the specified container)
  - docker run -v pathtofolderonlocalmachine:pathtofolderoncontainer -v /app/node_modules -p 3000:3000 -d --name node-app node-app-image (by -v /app/node_modules we are preventing this file from being changed if its copy on the host changed)
  - when production mode, there is no need for bind mode when meaning the -v volume.
  - inside the explorer of 50c80624:/app#,touch testfile, creating a file inside the container, if there is a volume so bind mode, this file will be created on the local machine.
  - docker run -v local:container:ro -v /app/node_modules etc.(:ro readonly so the container has no permission to edit the files that we gave it to it)
  - docker run -v local:container:ro --env PORT=4000 etc.
  - inside the explorer of 50c80624:/app#,
  - docker run -v local:container:ro --env-file ./.env etc.(`--env-file` setting the env)
  - docker volume ls(list the volume)docker volume ls, docker volume prune
  - docker volume prune (remove volume)
  - docker rm node-app -fv(remove it with volume) 
  - for -v (has many option types, if: is included, then local: container then it means which files are volumed from the host to the docker container, whether the for one docker container path mentioned it means the un volumed path in the docker container)
  - docker-compose up -d(build image and start the container)(executed accourding to our docker compose.yml)(search for an image that is named projectname_imagename, if it is exist then it will not build the image again)
  - docker-compose up --help(see what commands are available)
  - docker-compose down -v (the container is removed, -v removes volumes)
  - docker-compose up -d --build (so now the image will be built)
  - docker-compose -f basefile(-f for file) 
  - docker-compose -f docker-compose.yml -f docker-compose.dev.yml up -d (take all the configuration from the base file, and loud all the configurations from the dev file and see if there are any configurations that it needs to be overwritten)
  - docker-compose -f docker-compose.yml -f docker-compose.dev.yml up -d --build (--build for rebuilding the image)
  - in the Dockerfile, RUN npm install --only=production, so that prevent any devDependencies from being installed.("devDependencies":{"nodemon":"2.0.7"})
  - in the Dockerfile, ARG NODE_RUN if ["$NODE_ENV"= "DEVELOPMENT" ]; \ then npm install; \ else npm install --only=production; \ fi
  
  
  
  
  
  **docker-compose.yml:
   version: "3" (version of compose)
   services: (containers)  
    node-app:
      build: . (which image that we are going to use)(the image that is defined in the Dockerfile in the defined path)
        context: .(location of the Dockerfile)
        args:
          NODE_ENV: development
      ports: 
        - "3000:3000"
       volumes:
        - ./:/app
        - /app/node_modules
        environment:
          - PORT=3000
        command:
  
  
  **docker-compose.dev.yml:
        ports: 
        - "3000:3000"
       volumes:
        - ./:/app
        - /app/node_modules
        environment:
          - PORT=3000
        command:
          npm run dev
          
  **docker-compose.dev.yml:(for docker)
    version: "3"
    services: (containers)  
     node-app:
        build: .
        ports: 
        - "3000:3000"
        environment:
          - PORT=3000
        command:
          npm run dev
  
     mongo:
        image:mongo
        environment:
          - MONGO_INITDB_ROOT_USERNAME=username
          - MONGO_INITDB_ROOT_PASSWORD=mypassword
  
  
  
  
  
  **dockefile:
  FROM node:15
  WORKDIR /app
  COPY package.json .
  RUN npm install
  COPY . ./
  ENV PORT 3000
  EXPOSE $PORT
  CMD ["node","index.js"]
  
  
  - create dockerignore: nodemodules Dockerfile  .dockerignore  .git  .gitignore docker-compose*
  - in dockerignore we put: docker-compose*, (*) it means do not copy any file that starts with docker-compose
  - no need to copy node modules, it is just after copying the package,json, we run npm install inside the Dockerfile, the the modules will be installed.
  - EXPOSE 3000, has no impact does not open the port, just to tell the next person that we have the port 3000 that the container will be at.   
  - after each line and command of the above the docker will cach the changes and results
  - when you run docker build the first time, it will get through all the lines above starting from the FROM node etc.
  - the next time you run Docker, it will not take as much time as the first time since it cached all the results.
  - if step/layer three changes(we mean the package.json), we have to run from the layer three. 
  - CMD does in (run time, when we run the container) it says the command that is assigned to the container, when deploying our container it will run the writen command
  - RUN does in (build time)
  
  - COPY package.json . ,it means copy the package.json to the current directory that is set by WORKDIR 
  - COPY package.json /app , copy it to the /app directory
  
## Docker instructons:
  - Docker images are more like templates used to create a Docker container.
  - Docker Hub is a cloud-based repository in which users and partners could create, test, store and distribute container images.\
  - 
  - FROM Creates layers of dependencies like we could build an OS layer.`node:18.12.1`
  - RUN allows us to install your application and packages required for it.`npm install`
  - COPY adds files from Docker client’s current directory.`--chown=node:node . .`

    //grant permission of node project directory to node user
  - EXPOSE instruction informs Docker that the container listens on the specified network port at runtime.`3000`
  - WORKDIR setting working directory in the container `/home/node/app`
  - every time you run a command the command will be run from this directly, when you copy a file it will copy it from this directly.
  - USER create user in the docker image `node`
  - CMD specifies what command to run within the Container.`[ "npm", "start" ]`
  - COPY [–chown=<user>:<group>] [“<src>”,… “<dest>”] –chown is used to change the ownership of the file in the container filesystem. <user>:<group> is used to specify the user and group to whom we want to give the ownership.
  - The chown() function shall change the user and group ownership of a file. The path argument points to a pathname naming a file. The user ID and group ID of the named file shall be set to the numeric values contained in owner and group, respectively.
  
  
  
  Docker images are more like templates used to create a Docker container.
  
  
## fast programming shortcuts:
  - when taking an arg and its type you can use the arg as a type since you decleared its type.
  - `const data = header['apikey']?.tostring()`
  
  
## smart project assembly:
  - server.ts that contains the app.listen(port,()=>{Logger.info})
  - config.ts (has the db)


## mongoose database assembly declearation:
  - creating the database modeles 
  - for the lean() we put the interface in which the result shape will be.
## some of the main models functions that were used:
  - findByIdAndUpdate(blog._id,blog,{new: true})
  - exists:P<boolean>/new true means that the returned file will be the updated/
  - create,
  - findByIdAndRemove(id):P<Keystore | null>
  - deleteMany{client:client}
  - findOne({{ client: client, primaryKey: primaryKey, secondaryKey: secondaryKey, }})
  - 
  

## mongoDB, some of the main models functions that were used:
  - findByIdAndUpdate(blog._id,blog,{new: true})/new true means that the returned file will be the updated/
  - exists(filter):P<boolean>,create,
  - deleteMany(filter)
  - findByIdAndRemove(id)
  - findOne(filter)
  - .create(doc_schema_interface)
  - updateOne(filter,update,options)//update can be any
  findByIdAndUpdateobj or _UpdateQuery/inc,min,max,mul,rename,set,etc../ these are like conditions before updating comparing before and after data and if the condition is true then update.
  - findByIdAndUpdate(filter,update,)
  - fing(filter,projection,options)
  - collection.ensureIndex({"ingredients":"text"})
  
  
  
  
  
  - fing().populate().sort().limit().sort(S|{[key:S]:SortOrder| { $meta: 'textScore' } })
  - SortOrder = -1 | 1 | 'asc' | 'ascending' | 'desc' | 'descending';
  
## mongoDB functions and techniques:
  - const now = new Date();
  - remove(id):P<Keystore | null>-->findByIdAndRemove
  - asy fu removeAllForClient{client:client}-->deleteMany
  - findOne({{ client: client, primaryKey: primaryKey, secondaryKey: secondaryKey, }}), 
  - async function exists(id:Types.Object):promise<boolean>{const user=UserModel.exists(filter);return user !== null && user !== undefined}
  - findPrivateProfileById(id):promise<interface user|null>{return UserModel.findOne({ _id: id, status: true }) .select('+email') .populate({ path: 'roles', match: { status: true }, select: { code: 1 }, }) .lean<User>() .exec();}
  - findFieldsById(id,...fields:string[]):Promise<User | null>{return `UserModel.findOne`({ _id: id, status: true },[...fields]).lean().exec();}
  - async function create{founded_roles=find roles--> user.roles=[founded_roles]}
  - in KeystorModel, async function remove(id: Types.ObjectId):Promise<keystore | null>{
  return KeystoreModel.`findByIdAndRemove`(id).lean().exec();}
  - in KeystorModel, async function removeAllForClient(client: User){`KeystoreModel.deleteMany`({ client: client }).exec();}
  - db.collection.find( { $text: { $search: "bread beef" } }, { score: { $meta: "textScore" } } ).sort( { score: { $meta: "textScore" } } )
  - db.collection.find({ingredients:"beef"})
  - db.collections.find({ ingredients: { $all: ["beef","bread"] } })
  
  
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
   for example, Story `.find(...).populate({ path: 'fans', match: { age: { $gte: 21 }}, select: 'name -_id', options: { limit: 5 }`
   path is the name of the collection, match the specified condition, the results will be showed accourding to, options here we said we only want 5 results to be showed.
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
  -  .lean() converts mongoose.Document to Plain Javascript Object (for mongodb create method, you must use .Object() because mongodb returns a document)
  - Pre-save hooks in mongoose.js
  - Lean(), By default, Mongoose queries return an instance of the Mongoose Document class. Documents are much heavier than vanilla JavaScript objects, because they have a lot of internal state for change tracking. Enabling the lean option tells Mongoose to skip instantiating a full Mongoose document and just give you the POJO.
  - select:false, Currently, if we use select: false in a schema, we need to do Model.findOne({ _id }).select("+field_a") to get its value.
  
## basics i learned:
  - we import the other index files that have general default export for the initializing purpose,so the exported item will be established without being used in the importing file, for instance, redis,database, 
  - A network socket is a software structure within a network node of a computer network that serves as an endpoint for sending and receiving data across the network. The structure and properties of a socket are defined by an application programming interface (API) for the networking architecture. Sockets are created only during the lifetime of a process of an application running in the node.Normally, a server runs on a specific computer and has a socket that is bound to a specific port number. The server just waits, listening to the socket for a client to make a connection request.
  - We used private.key to sign JWT and public.key to verify it
  - modal.create, Creates a new document or documents.
  - process.exit(1) exit with failure error, while the process.exit(0) exits without
  - The connection process in Mongoose inherits the Node EventEmitter class
  - Currently errors are first forwarded to the process.on('error') event before reaching process.on('uncaughtException')
  - emitter.on(event,listner)
  - extends keyof usage, public property names of T
  - Abstract classes are mainly for inheritance where other classes may derive from them.
  - CORS Configuration Options: origin(Configures the Access-Control-Allow-Origin CORS header), methods(Configures the Access-Control-Allow-Methods CORS header), allowedHeaders(ex: ['Content-Type', 'Authorization']), exposedHeaders, optionsSuccessStatus: a status code for successful OPTIONS requests, since some legacy browsers (IE11, various SmartTVs) choke on 204.
  
  
  

  
  
  
  - declearing global in typescript
  - Running tsc locally will compile the closest project defined by a tsconfig.json, you can compile a set of TypeScript files by passing in a glob of files you want.
  - (TLS) is a Transport Layer Security, it encrypts data sent over the Internet to ensure that eavesdroppers and hackers are unable to see what you transmit which is particularly useful for private and sensitive information such as passwords, credit card numbers, and personal correspondence.
  - winston is designed to be a simple and universal logging library with support for multiple transports, https://github.com/winstonjs/winston
  - baseUrl tells Typescript to base all your modules from a specific directory. Which means they will also be compiled with paths relative to baseUrl, but node is not aware of tsconfig.json, so it will try to find these modules in the path relative to the script and relative to NODE_PATH env variable, So the solution would be to pass NODE_PATH before running, like this:```git npx tsc && NODE_PATH=src node src/main/app.ts```
  - var outside->glob, inside->local (updated,undefined before declecration)
  - let(improved var {block scoped})(updated,same name decleared in another scope,error before declecration)
  - const has the let properties, but not updated
  
  
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
## Files distribution and classification:  
  - in the core folder we put the ApiError, ApiResponse,Logger{init,params,const dailyRotateFile = new DailyRotateFile},JWT{class JwtPayload} and utils{API_KEY,AUTHORIZATION}.
  - in the config file we have {caching,redis,tokenInfo,corsUrl,db}
  - 
  
  
  
## functions i learned:
  - The 'uncaughtException' event is emitted when an uncaught JavaScript exception bubbles all the way back to the event loop. By default, Node.js handles such exceptions by printing the stack trace to stderr and exiting with code 1, overriding any previously set process.exitCode. Adding a handler for the 'uncaughtException' event overrides this default behavior. Alternatively, change the process.exitCode in the 'uncaughtException' handler which will result in the process exiting with the provided exit code. Otherwise, in the presence of such handler the process will exit with 0.
  
  
  - the next() is a function that takes any, as shown in the following: (err?: any): void
  - in side the namespace NodeJS, we have process extends EventEmitter, and redecleared it again.
  - Process events({'beforeExit' makes asynchronous calls},)
  - Process.on(uncaughtException(uncaught exception bubbles to the event loop), unhandledRejection (The latter is used when an exception happens in a Promise based async context)
  - uncaughtException(uncaught exception bubbles to the event loop), unhandledRejection (The latter is used when an exception happens in a Promise based async context)
  - winston (one of the most popular logging library),(Use Structured Logging),post-mortem debugging
  - lib.es5{the most famous used interface is Error interface}
  - events.d.ts{global{var process=NODEJS.process;namespace NODEJS{interface Process extends EventEmitter}}}, process.d.ts{global{namespace NODEJS{EventEmitter{on}}}
  - inside the global redeclearing the namespace of NODEJS to declear new contents.
  - Symbol has no constructor function, (Symbols also work as property keys, but are not iterable, which is great for serialisation)
  - Global symbols registry, There’s a global symbols registry that allows you to access tokens across your whole application.
  - 
  
  
  
  
  
  
  
  - express --> Application--> EventEmitter, IRouter, Express.Application
  - listen was taken from the application, and on was taken from the EventEmitter which Application is extending.
  - express is a function that returns an interface of `export interface Express extends Application { request: Request; response: Response; }`
  - Application extends EventEmitter, Express.Application, IRouter
  -  interface IRouter extends RequestHandler{all,patch,options,head,connect,checkout,mkactivity,copy,merge}
  - the express had been decleared as a functions `declare function e(): core.Express;`
  and then we added new variables as the name of it (e) `declare namespace e {IRouterHandler}`, when we namef space with the same name `application,request,response,static,urlencoded,query,Router`
  - express.use is one of IRouterHandler interface items, as `(...handlers: Array<RequestHandler<RouteParameters<Route>>>): T;`
  - inside var json: typeof bodyParser.json;, bodyParser.json is `json(options?: OptionsJson): NextHandleFunction;`, becuase inside the bodyParser.json there is `json(options?: OptionsJson): NextHandleFunction;`
  - so we can see that use can take a request handler which returns a void at the end.
  - express.json takes the OptionsJson that has {reviver,strict} which extends interface options which has {inflate,limit,type,verify}.
  - app.use(express.urlencoded), inside the namespace e we have the urlencoded that takes different parameters and returns RequestHandler.
  - app.use is used because the of the IRouterMatcher that is existing inside the namespace of e.
  - becuase of the IRouterMatcher that is contained inside the namespace e, the express().use can take two variables, the path and the requesthandler.
  - because of that e extend application, we can do the express().listen(),we have multi options,
  - .listen(port/path,callback):http.Server
  - .listen(port,hostname,backlog/callback,callback?):http.Server
  - .listen(handle:any,listeningListener:?()):http.Server
  - first, NetServer or Server extends EventEmitter{constructor(options?: ServerOpts,connectionListener?:(socket:Socket)=>void);}.(from the net.d.ts file) it has members like on,listen,close,address,getConnections,emit,once,prependListener,tener(event:S,list:(...args)=>void)
  - (from http.Server) http.Server is a class<req,res> extends NetServer, as the netserver its 
  - http.Server's constructors are:
       . constructor(requestListener?: RequestListener<Request, Response>)
       . constructor(options: ServerOptions<Request, Response>, requestListener?: RequestListener<Request, Response>);
  - NetServer or Server's constructors are:
      .  constructor(connectionListener?: (socket: Socket) => void);
      .  constructor(options?: ServerOpts, connectionListener?: (socket: Socket) => void);
  - Logger(options?:LoggerOptions) is a function from the winston 
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  - The keyword async before a function makes the function return a promise, for instance,async function myFunction() { return "Hello"; }Is the same as: function myFunction() { return Promise.resolve("Hello"); }
  - Object.keys (return an array whose elements are strings corresponding to the enumerable properties found directly upon an object.)
  for instance, var object = { 70: 'x', 21: 'y' };-->['21', '35'].
  - Object.entries (static method returns an array of a given object's own enumerable string-keyed property key-value pairs.)
  for instance, const obj = { 0: "a", 1: "b",};-->Object.entries(obj) // [ ['0', 'a'], ['1', 'b'], ['2', 'c'] ]
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
  - Object.assign(target, ...sources), it copies the values and properties from one or more source objects to a target object, and overwrite the already existing keys.
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
  - from the bson.d.ts declare class ObjectId{constructor(inputId?: string | number | ObjectId | ObjectIdLike | Buffer | Uint8Array);}
  - from the lodash library .pick(user, ['_id', 'name', 'email']){Creates an object composed of the picked `object` properties.}
  == is used for comparison between two variables irrespective of the datatype of variable.
  === is used for comparision between two variables but this will check strict type, which
  for example in the authorization we did the if (roles.length === 0) the same type
  ("0"==0)-->True, (0n==0)-->True
  - reading TypeScript catalogs: we did the .updateOne({ _id: user._id }, { $set: { ...user } })
  .updateOne(_FilterQuery<T>, { $set: { ...user } })
  type _FilterQuery<T>={[P in keyof T]?: Condition<T[P]>;} & RootQuerySelector<T>;
  it is saying that _FilterQuery can be any key of T which is the module
  | means or, & means and
  condition could be T | T[] | $eq, $gt, $gte, $in etc.
  - the type of the object and its indexes we mean the keys, are defined as followed //: { [key: string]: string }//
  - The res.append() function appends the specified value to the HTTP response header field and if the header is not already set then it creates the header with the specified value. 
  
  
  

## basic concepts everyone should know it:
  
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
  - The difference between Post and Get is :
  - Regarding Get: values are visible in the URL, limitation on the length of the values, generally 255 characters, performs are better compared to POST because of the simple nature of appending the values in the URL, supports only string data types, can be bookmarked, request is often cacheable, Parameters remain in web browser history.
  - Regarding Post: values are not visible in the URL, no limitation on the length of the values since they are submitted via the body of HTTP, lower performance as compared to GET method because of time spent in including POST values in the HTTP body, supports different data types, such as string, numeric, binary.., no bookmark, request is hardly cacheable, Parameters are not saved in web browser history.
  - In general, the HTTP PUT method is used to update an existing resource, while the POST method is used to create a new resource.
  - A credential is a data structure(a user name and password authentication token) that provides proof of an application's claim to a principal name. An application uses a credential to establish its global identity.
  
  
  
  
  
  
  
  
  
  
  
  
  
  
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
     
 
