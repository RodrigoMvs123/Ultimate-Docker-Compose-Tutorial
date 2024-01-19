# Ultimate-Docker-Compose-Tutorial

https://www.youtube.com/watch?v=SXwC9fSwct8 

What is Docker Compose ?

Front End / Back End / Database 

API / Authentication Service / Database 2 

Microservice App
An application broken down into multiple micro services 
They must deployed and run together 
The services need to communicate 

Docker Compose
Define and Run Multiple Services and Applications that belong together in one environment 
Multi-Container Docker Applications

Start 2 Docker Containers
Using Docker Commands 
Create a Docker Network 
Start MongoDB Container
Start Mongo Express Container 

Install Docker 
https://docs.docker.com/desktop/install/windows-install/ 

Terminal Warp
https://www.warp.dev/ 

Docker MongoDB Container 

docker network create mongo-network ( Allow containers to communicate with each other and with the external world )
docker network ls
docker run -d \ 

https://hub.docker.com/_/mongo 

-p 27017:27017

Environment Variables 
-e MONGO_INITDB_ROOT_USERNAME=admin \ 
-e MONGO_INITDB_ROOT_PASSWORD=supersecret \ 

- -network mongo-network \
- -name mongodb \
mongo ( Specify the Image ) 

docker ps

Docker Mongo Express
https://hub.docker.com/_/mongo-express 

Terminal Warp
docker run -d \ 
-p 8081:8081

Environment Variables 

-e ME-CONFIG_MONGODB_ADMINUSERNAME=admin \ 
-e ME_CONFIG_MONGODB_ADMINPASSWORD=supersecret \ 
-e ME_CONFIG_MONGODB_SERVER=mongodb \

- -network mongo-network \
- -name mongo-express \
mongo-express ( Specify the Image ) 

docker ps
… Docker ID
…

You can hit at localhost:8081

docker logs ( Docker ID )
… Mongo Express server listening at http://0.0.0.0:8081
… basicAuth credentials are “admin:pass” … 

localhost:8081 
Log In
Username: admin 
Password: pass

Terminal Warp
docker ps
CONTAINER ID    IMAGE      COMMAND     CREATED     STATUS    PORTS    NAME  …
…
…

Docker Compose YAML File
You use a single YAML file to configure and maintain your applications´s services 
With a single command, you create and start all the services from your configuration
Declarative definition

Map commands to Compose definition 

## start mongodb
docker run -d \ 
-p 27017:27017
-e MONGO_INITDB_ROOT_USERNAME=admin \
-e MONGO_INITDB_ROOT_PASSWORD=password \
- -net mongo-network \
- -name mongodb \
mongo 

Compose yaml

version: ‘3.1’             ( Version of Docker Compose ) 

services: 
    mongodb:              ( Container Name ) 
        image: mongo

        ports:
27017:27017
(HOST)(CONTAINER)
        environment:
          - MONGO…_USERNAME=admin  
          - MONGO…_PASSWORD=secret

## start mongo-express
docker run -d \
-p 8081:8081 \
-e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
-e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
-e ME_CONFIG_MONGODB_SERVER=mongodb \
- -net mongo-network \
- -name mongo-express \ 
mongo-express 

Compose yaml 

version: ‘3.1’

services: 
    mongodb:
    …

    mongo-express:        ( Container Name ) 
        image: mongo-express
        ports: 
 - 8081:8081
        environment: 
 - ME_CONFIG_MONGODB_A…
…

Docker Compose Yaml with Multiple Containers 

version: ‘3’ 
services: 
    mongodb:
        image: mongo
        ports:
            -27017:27017
        environment:
            - MONGO_INITDB_ROOT_USERNAME=admin 
            - MONGO_INITDB_ROOT_PASSWORD=password 
 
    mongo-express: 
        image: mongo-express
        ports: 
            - 8081:8081
        environment:
            -ME_CONFIG_MONGODB_ADMINUSERNAME=admin 
            -ME_CONFIG_MONGODB_ADMINPASSWORD=password 
            - ME_CONFIG_MONGODB_SERVER=mongodb 

“ X as Code” Concept
Codes that defines how your services should run 
Code can be versioned 
Easier collaboration 

Network Configuration
By default, Compose sets up a single network for your app
Communication via Container Name
But option to specify your own networks with the top-level “networks” key

Create Compose File 

Visual Studio Code
Explorer
Open Editors 
mongo-services.yaml

mongo-services.yaml
version: ‘3.1’ 
services: 
    mongodb:
        image: mongo
        ports:
            -27017:27017
        environment:
            - MONGO_INITDB_ROOT_USERNAME: admin 
            - MONGO_INITDB_ROOT_PASSWORD: supersecret 
 
    mongo-express: 
        image: mongo-express
        ports: 
            - 8081:8081
        environment:
            -ME_CONFIG_MONGODB_ADMINUSERNAME: admin 
            -ME_CONFIG_MONGODB_ADMINPASSWORD: supersecret  
            - ME_CONFIG_MONGODB_SERVER: mongodb 

Terminal Warp
docker ps
CONTAINER ID    IMAGE      COMMAND     CREATED     STATUS    PORTS    NAME  …
…
…
docker stop CONTAINER ID CONTAINER ID 
…
…
docker rm CONTAINER ID CONTAINER ID 
…
…
docker network rm mongo-network
…
docker network ls
…
…
docker-compose -f mongo-services.yaml up
[+] Running 3/0
Network projects default                      Created
Container projects-mongodb-1            Created
Container projects-mongo-express-1  Created
…

Terminal Warp
docker network ls
NETWORK ID    NAME                   DRIVER    SCOPE
…                        projects-default     bridge       local
…

Terminal Warp
docker ps
CONTAINER ID    IMAGE      COMMAND     CREATED     STATUS    PORTS    

NAME  …
projects-mongo-express-1                                                                                                                                    
projects-mongodb-1 

Docker Container -> Docker Container ( Initial Connection ) 
Web App that needs to access a database ( JS )

Visual Studio Code
Explorer
Open Editors 
mongo-services.yaml

mongo-services.yaml
version: ‘3.1’ 
services: 
    mongodb:
        image: mongo
        ports:
            - 27017:27017
        environment:
            - MONGO_INITDB_ROOT_USERNAME: admin 
            - MONGO_INITDB_ROOT_PASSWORD: supersecret 
 
    mongo-express: 
        image: mongo-express
        ports: 
            - 8081:8081
        environment:
            -ME_CONFIG_MONGODB_ADMINUSERNAME: admin 
            -ME_CONFIG_MONGODB_ADMINPASSWORD: supersecret  
            - ME_CONFIG_MONGODB_SERVER: mongodb
        depends_on:
- “mongodb”
 
localhost:8081
Mongo Express
Database Name
my-db
Create Database
Collection Name
my-collection
Create Collection

Terminal Warp
docker-compose -f mongo-services.yaml up 
…
…
…
^CGracefully stopping… ( press Ctrl+C again to force )
[+] Stopping 0/0
    Container projects-mongo-express-1     Stopping
[+] Stopping 2/2 projects-mongodb-1         Stopping
    Container projects-mongo-express-1     Stopped
    Container projects-mongodb-1               Stopped
…
docker-compose -f mongo-services.yaml up -d 
[+] Running 2/2
    Container projects-mongo-express-1     Started
    Container projects-mongodb-1               Started
docker ps
…
…
docker-compose -f mongo-services.yaml down
[+] Running 3/2
    Container projects-mongo-express-1     Removed
    Container projects-mongodb-1               Removed
    Network projects-default                         Removed
docker ps -a ( Stopped and Running Containers )
CONTAINER ID    IMAGE      COMMAND     CREATED     STATUS    PORTS    NAME  …

Containers are Ephemeral 
By default, no persistence
All data is gone, when containers are removed
Volumes are the mechanism for persisting data generated by and used by containers

You want to stop and re-start the container only
Stopping containers will keep the data 

Terminal Wrap

docker-compose -f mongo-services.yaml up -d 
[+] Running 3/3
    Network projects-default                         Created
    Container projects-mongodb-1               Started
    Container projects-mongo-express-1      Started 
docker-compose -f mongo-services.yaml stop
[+] Stopping 2/2
    Container projects-mongo-express-1     Stopped
    Container projects-mongodb-1               Stopped
docker ps -a ( Stopped and Running Containers )
CONTAINER ID    IMAGE      COMMAND     CREATED     STATUS    PORTS    NAME  …
                                                                                             Exited
                                                                                             Exited 
docker-compose -f mongo-services.yaml start 
[+] Running 2/2
    Container projects-mongodb-1               Started
    Container projects-mongo-express-1      Started 

Add own web application

To Connect
mongo-express                     my-app ( JS )
                            mongodb

localhost:8081
Mongo Express

Viewing Database: my-db
Collections
    delete_me
    my-colllections
Viewing Collection: my-collection
New Document 
Add Document 
{
        “_id”: ObjectId(),
        “myid”: 1,
        “data”: “some dynamic data loaded from DB”
}
Save

_id                       myid            data
…                        1                  some dynamic data loaded from DB

JS Application
Connects to the database
Displays the retrieved data
Containerize with Dockerfile
Run it as part of the Docker Compose 

To Clone the Application
GitLab Repository 
https://gitlab.com/twn-youtube/docker-compose-crash-course 

Terminal Wrap
ls
… docker-compose-crash-course    mongo-services.yaml       terraform-eks
cd docker-compose-crash-course/
~/Projects/docker-compose-crash-course git:(master) 

Visual Studio Code
Explorer 
Open Editors 
app
index.html

index.html
<html lang="en">
<style>
    .container {
        margin: 40px auto;
        width: 80%;
    }
 
    hr {
        width: 400px;
        margin-left: 0;
    }
    h3 {
        display: inline-block;
    }
    #static {
        color: red;
    }
    #dynamic {
        color: green;
    }
</style>
<script>
    (async function init() {
        const cont = document.getElementById('container');
        const response = await fetch('http://localhost:3000/fetch-data', {
            method: "GET",
            headers: {
              'Accept': 'application/json',
              'Content-Type': 'application/json'
            }
        });
        const jsonResponse = await response.json();
        document.getElementById('dynamic').textContent = jsonResponse.data;
    })();
</script>
<body>
    <div class='container' id='container'>
        <h1>Welcome to this Super Sophisticated App</h1>
        <span>Some static data:  </span><h3 id="static">this is hard-coded in index.html</h3>
        <hr />
        <span>Data from MongoDB:  </span><h3 id="dynamic"></h3>
        <hr />
    </div>
</body>
</html>

Visual Studio Code
Explorer 
Open Editors 
app
index.html
server.js

server.js
let express = require('express');
let path = require('path');
let fs = require('fs');
let MongoClient = require('mongodb').MongoClient;
let bodyParser = require('body-parser');
let app = express();

const DB_USER = process.env.MONGO_DB_USERNAME
const DB_PASS = process.env.MONGO_DB_PWD

app.use(bodyParser.urlencoded({
  extended: true
}));
app.use(bodyParser.json());

app.get('/', function (req, res) {
    res.sendFile(path.join(__dirname, "index.html"));
  });

// when starting app locally, use "mongodb://admin:password@localhost:27017" URL instead
let mongoUrlDockerCompose = `mongodb://${DB_USER}:${DB_PASS}@mongodb`;

// pass these options to mongo client connect request to avoid DeprecationWarning for current Server Discovery and Monitoring engine
let mongoClientOptions = { useNewUrlParser: true, useUnifiedTopology: true };

// the following db and collection will be created on first connect
let databaseName = "my-db";
let collectionName = "my-collection";

app.get('/fetch-data', function (req, res) {
  let response = {};
  MongoClient.connect(mongoUrlDockerCompose, mongoClientOptions, function (err, client) {
    if (err) throw err;

    let db = client.db(databaseName);

    let myquery = { myid: 1 };

    db.collection(collectionName).findOne(myquery, function (err, result) {
      if (err) throw err;
      response = result;
      client.close();

      // Send response
      res.send(response ? response : {});
    });
  });
});

app.listen(3000, function () {
  console.log("app listening on port 3000!");
});

Visual Studio Code
Explorer 
Open Editors 
Dockerfile 

Dockerfile 
FROM node:20-alpine

RUN mkdir -p /home/app

COPY ./app /home/app

# set default dir so that next commands executes in /home/app dir
WORKDIR /home/app

# will execute npm install in /home/app because of WORKDIR
RUN npm install

# no need for /home/app/server.js because of WORKDIR
CMD ["node", "server.js"]

Visual Studio Code
Explorer 
Open Editors 
app
index.html
server.js
package.json

package.json 
{
  "name": "developing-with-docker",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js"
  },
  "author": "Nana Janashia",
  "license": "ISC",
  "dependencies": {
    "body-parser": "^1.20.2",
    "express": "^4.18.2",
    "mongodb": "^4.16.0"
  }
}

Terminal Wrap
ls
… docker-compose-crash-course    mongo-services.yaml       terraform-eks
cp mongo-services.yaml docker-compose-crash-course/mongo-services.yaml

Visual Studio Code
Explorer
Open Editors
mongo-services.yaml

mongo-services.yaml
version: '3.1'
services: 
    my-app: 
        build: .
        ports:
            - 3000:3000
        environment:
            - MONGO_DB_USERNAME: admin 
            - MONGO_DB_PWD: supersecret 

    mongodb:
        image: mongo
        ports:
            - 27017:27017
        environment:
            - MONGO_INITDB_ROOT_USERNAME: admin 
            - MONGO_INITDB_ROOT_PASSWORD: supersecret 
 
    mongo-express: 
        image: mongo-express
        ports: 
            - 8081:8081
        environment:
            -ME_CONFIG_MONGODB_ADMINUSERNAME: admin 
            -ME_CONFIG_MONGODB_ADMINPASSWORD: supersecret  
            - ME_CONFIG_MONGODB_SERVER: mongodb 
        depends_on:
            - "mongodb"

Terminal Wrap

docker ps
CONTAINER ID    IMAGE      COMMAND     CREATED     STATUS    PORTS    NAME  …
…
…
docker-compose -f mongo-services.yaml stop
[+] Stopping 2/2
    Container projects-mongo-express-1     Stopped
    Container projects-mongodb-1               Stopped
cd docker-compose-crash-course/
~/Projects/docker-compose-crash-course git:(master)+1
docker-compose -f mongo-services.yaml up -d
[+] Running 4/4
Network docker-compose-crash-course_default                          Created
Container docker-compose-crash-course-my-app-1                    Started
Container docker-compose-crash-course-mongodb-1                 Started
Container docker-compose-crash-course-mongo-express-1       Started
~/Projects/docker-compose-crash-course git:(master)+1
docker ps
CONTAINER ID    IMAGE      COMMAND     CREATED     STATUS    PORTS    NAME  …
…
…
~/Projects/docker-compose-crash-course git:(master)+1
docker ps -a 
…
…
…
~/Projects/docker-compose-crash-course git:(master)+1
docker-compose -f mongo-services.yaml down
[+] Running 4/3
Container docker-compose-crash-course-my-app-1                    Removed
Container docker-compose-crash-course-mongodb-1                 Removed
Container docker-compose-crash-course-mongo-express-1       Removed
Network docker-compose-crash-course_default                          Removed
~/Projects/docker-compose-crash-course git:(master)+1
docker ps -a 
…
…
~/Projects/docker-compose-crash-course git:(master)+1
docker-compose - -project-name projects -f mongo-services.yaml up -d ( Overwritten ) 
[+] Running 3/3
Container docker-compose-crash-course-my-app-1                    Started
Container docker-compose-crash-course-mongodb-1                 Started
Container docker-compose-crash-course-mongo-express-1       Started
~/Projects/docker-compose-crash-course git:(master)+1
docker ps -a 
…
…
…
~/Projects/docker-compose-crash-course git:(master)+1

localhost:8081
Mongo Express

Databases
my-db
Viewing Database: my-db
Collections
    my-colllections
_id                 myid          data
..                    1                some dynamic data loaded from DB

localhost:3000
Welcome to this Super Sophisticated App
…
…

Visual Studio Code
Explorer
Open Editors
docker-compose.yaml

docker-compose.yaml
version: '3'
services:
  my-app:
    # build: .
    image: docker-hub-user/image-name:image-tag
    ports:
     - 3000:3000
    environment:
     - MONGO_DB_USERNAME=${MONGO_ADMIN_USER}
     - MONGO_DB_PWD=${MONGO_ADMIN_PASS}
  mongodb:
    image: mongo
    ports:
     - 27017:27017
    environment:
     - MONGO_INITDB_ROOT_USERNAME=${MONGO_ADMIN_USER}
     - MONGO_INITDB_ROOT_PASSWORD=${MONGO_ADMIN_PASS}
  mongo-express:
    image: mongo-express
    restart: always
    ports:
     - 8081:8081
    environment:
     - ME_CONFIG_MONGODB_ADMINUSERNAME=${MONGO_ADMIN_USER}
     - ME_CONFIG_MONGODB_ADMINPASSWORD=${MONGO_ADMIN_PASS}
     - ME_CONFIG_MONGODB_SERVER=mongodb
    depends_on:
     - "mongodb"

Terminal Wrap

~/Projects/docker-compose-crash-course git:(master)+1
docker-compose -f mongo-services.yaml stop
WARN [0000] The “MONGO_ADMIN_USER” variable is not set. Defaulting to a black string.
…
~/Projects/docker-compose-crash-course git:(master)+1
export MONGO_ADMIN_USER=admin 
~/Projects/docker-compose-crash-course git:(master)+1
export MONGO_ADMIN_PASS=supersecret
~/Projects/docker-compose-crash-course git:(master)+1
docker-compose -f mongo-services.yaml -p projects stop
[+] Running 3/3
Container docker-compose-crash-course-my-app-1                    Stopped
Container docker-compose-crash-course-mongodb-1                 Stopped
Container docker-compose-crash-course-mongo-express-1       Stopped

localhost:3000
This website can not be reached 

~/Projects/docker-compose-crash-course git:(master)+1
docker-compose -f mongo-services.yaml -p projects start
Container docker-compose-crash-course-my-app-1                    Started
Container docker-compose-crash-course-mongodb-1                 Started
Container docker-compose-crash-course-mongo-express-1       Started

localhost:3000
Welcome to this Super Sophisticated App

Use Secrets in Docker Compose
“Secrets” is an even better way to use secrets without having to use environment variables

services: 
     myapp:
         image: myapp:latest
         secrets:
            - my-secret

secrets: 
    my_secret:
        file: ./my_secret.txt

Define the secret using the top-level secrets element
In the service definition, reference the secret with the secrets attribute
This permits granular access control within a service container via standard filesystem permissions 

Reference Image from Private Repository 

Docker Image Stored in Private Repository
Build Image
Push to Docker Repository 

Docker Hub
https://hub.docker.com/ 

Create Private Repository
Repository Name
myapp
Create

Terminal Wrap

~/Projects/docker-compose-crash-course git:(master)+1
ls
… Dockerfile      README.md      app      mongo-services.yaml
~/Projects/docker-compose-crash-course git:(master)+1
docker build -t rodrigomvs123/myapp:1.0 .
…
~/Projects/docker-compose-crash-course git:(master)+1
docker images
… REPOSITORY
… rodrigomvs123/my-app
~/Projects/docker-compose-crash-course git:(master)+1
docker login 
… Username: rodrigomvs123
… Password: …
~/Projects/docker-compose-crash-course git:(master)+1
docker push rodrigomvs123/myapp:1.0 
~/Projects/docker-compose-crash-course git:(master)+1

Wrap UI
Work Drive
Personal
New Workflow
docker-push
echo {{argument_1}} | docker login -u rodrigomvs123 - -password-stdin
docker build -t rodrigomvs123/my-app:{{argument_2}} .
docker push  rodrigomvs123/my-app:{{argument_2}} 
                   
                                                                1.0
                                                                        Save workflow 
Work Drive
Personal
docker-push

Terminal Wrap

~/Projects/docker-compose-crash-course git:(master)+1
echo password | docker login -u rodrigomvs123 - -password-stdin
docker build -t rodrigomvs123/my-app:1.1 .
docker push  rodrigomvs123/my-app:1.1 

Visual Studio Code
Explorer
Open Editors
mongo-services.yaml

mongo-services.yaml
version: '3'
services:
  my-app:
    # build: .
    image: docker-hub-user/image-name:image-tag # rodrigomvs123/my-app:1.0
    ports:
     - 3000:3000
    environment:
     - MONGO_DB_USERNAME=${MONGO_ADMIN_USER}
     - MONGO_DB_PWD=${MONGO_ADMIN_PASS}
  mongodb:
    image: mongo
    ports:
     - 27017:27017
    environment:
     - MONGO_INITDB_ROOT_USERNAME=${MONGO_ADMIN_USER}
     - MONGO_INITDB_ROOT_PASSWORD=${MONGO_ADMIN_PASS}
  mongo-express:
    image: mongo-express
    restart: always
    ports:
     - 8081:8081
    environment:
     - ME_CONFIG_MONGODB_ADMINUSERNAME=${MONGO_ADMIN_USER}
     - ME_CONFIG_MONGODB_ADMINPASSWORD=${MONGO_ADMIN_PASS}
     - ME_CONFIG_MONGODB_SERVER=mongodb
    depends_on:
     - "mongodb"

Terminal Wrap

~/Projects/docker-compose-crash-course git:(master)+1
docker-compose -f mongo-services.yaml -p projects stop
[+] Running 3/3
Container docker-compose-crash-course-my-app-1                    Stopped
Container docker-compose-crash-course-mongodb-1                 Stopped
Container docker-compose-crash-course-mongo-express-1       Stopped
~/Projects/docker-compose-crash-course git:(master)+1
docker ps 
CONTAINER ID    IMAGE      COMMAND     CREATED     STATUS    PORTS    NAME  …

~/Projects/docker-compose-crash-course git:(master)+1
docker ps -a
CONTAINER ID    IMAGE      COMMAND     CREATED     STATUS    PORTS    NAME  …
…
…
…
~/Projects/docker-compose-crash-course git:(master)+1
docker rm “CONTAINER ID” # projects-my-app
~/Projects/docker-compose-crash-course git:(master)+1
docker-compose -f mongo-services.yaml -p projects up -d
[+] Running 3/3
Container docker-compose-crash-course-my-app-1                    Started
Container docker-compose-crash-course-mongodb-1                 Started
Container docker-compose-crash-course-mongo-express-1       Started
~/Projects/docker-compose-crash-course git:(master)+1
[+] Running 1/1 
my-app Pulled 
Container projects-my-app-1                    Started
Container projects-mongodb-1                 Started
Container projects-mongo-express-1       Started
~/Projects/docker-compose-crash-course git:(master)+1
docker ps -a
CONTAINER ID    IMAGE      COMMAND     CREATED     STATUS    PORTS    
NAME  …
rodrigomvs123/my-app:1.0
…
~/Projects/docker-compose-crash-course git:(master)+1

localhost:3000
Welcome to this Super Sophisticated App
…
…

Kubernetes to the Rescue 
Can manage large-scale apps and containers deployed across multiple nodes
Auto-Scaling 

