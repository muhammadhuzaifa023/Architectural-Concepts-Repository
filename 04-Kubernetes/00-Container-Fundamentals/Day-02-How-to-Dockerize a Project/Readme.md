## How To Dockerize a Project

## If you would like to use a Docker or Kubernetes sandbox instead of a local setup, you can use the below:
- Docker playground: https://labs.play-with-docker.com/
  ## Dockerizing a Project – Visual Reference using Docker Sandbox
  ![Docker Image](Images/Docker%20Image1.png)
  <br/>
  ![Docker Image](Images/Docker%20Image2.png)
 - This is the Docker Repository where we keep all Docker Images. These images are pulled by the Dev, Test, and Prod environments.
<br>

![Docker Image](Images/Docker%20Image3.png)

- Kubernetes playground: https://killercoda.com/playgrounds/scenario/kubernetes

## Download Docker desktop client
```
https://www.docker.com/products/docker-desktop/
```

## Get started
- Clone a sample git repository using the below command or use your project for the demo:

```
git clone https://github.com/docker/getting-started-app.git
```
![Docker Image](Images/Docker%20Image4.png)
<br>

![Docker Image](Images/Docker%20Image5.png)

- cd into the directory
```
cd getting-started-app/
```
<br>

![Docker Image](Images/Docker%20Image6.png)
- Create an empty file with the name Dockerfile
```
touch Dockerfile
```
<br>

![Docker Image](Images/Docker%20Image7.png)
- Using the text editor of your choice, paste the below content (each instruction is explained in the video)
- Here in dockerfile we used the term FROM node so node is the image which provide by the DockerHub
![Docker Image](Images/Docker%20Image8.png)
<br>

```dockerfile
FROM node:22-alpine
WORKDIR /app
COPY . .
RUN npm install --omit=dev
CMD ["node", "src/index.js"]
EXPOSE 3000
```
<br>

![Docker Image](Images/Docker%20Image9.png)

Detailed Explanation of Dockerfile Instructions

1. FROM node:22-alpine

Purpose: Selects the Base Image

FROM defines the base image on which our Docker image will be built.

node:22-alpine means we are using Node.js version 22.

Alpine is a lightweight Linux distribution.

It provides Node.js and the required runtime environment without needing to install Node.js manually.

Why do we use it?

Instead of installing Node.js and configuring the environment ourselves, Docker provides a ready-made Node.js environment through this base image.

Remember: FROM is the starting point of every Dockerfile (except scratch-based images).

2. WORKDIR /app

Purpose: Sets the Working Directory

WORKDIR creates or selects a directory inside the container.

/app is the directory where our application will reside.

All subsequent relative paths will be executed from this directory.

Why do we use it?

It keeps our application files organized in a dedicated directory instead of placing everything in the container's root directory.

Example:

After executing this instruction, commands such as COPY, RUN, and CMD will use /app as their working directory when relative paths are provided.

3. COPY . .

Purpose: Copies Application Files into the Container

The COPY instruction transfers files and directories from the build context (usually your project directory) into the Docker image.

Understanding the two dots:

First . = Current directory on the host machine (build context).

Second . = Current working directory inside the container (/app).

Example:

If your project contains:

my-node-app/
├── src/
│   └── index.js
├── package.json
├── package-lock.json
└── Dockerfile

Docker copies these files into /app inside the container.

Why do we use it?

The application source code and configuration files need to be available inside the container so that Node.js can execute the application.

Important: Files excluded through .dockerignore will not be copied into the build context.

4. RUN npm install --omit=dev

Purpose: Installs Application Dependencies During Image Build

RUN executes a command while building the Docker image.

npm install installs the dependencies defined in package.json.

--omit=dev skips installing packages listed under devDependencies.

Understanding npm dependencies:

In a Node.js project, dependencies are generally divided into two categories:

dependencies: Packages required to run the application in production.

devDependencies: Packages required mainly during development, testing, and building.

Example:

{
  "dependencies": {
    "express": "^4.21.0"
  },
  "devDependencies": {
    "nodemon": "^3.1.0"
  }
}

With npm install --omit=dev:

Express will be installed.

Nodemon will not be installed.

Why do we use it?

Production containers should avoid unnecessary development packages. This helps reduce the installed dependencies and can make the resulting image smaller.

Remember: RUN executes during image building, not every time the container starts.

5. CMD ["node", "src/index.js"]

Purpose: Defines the Default Application Startup Command

CMD specifies the default command that executes when a container starts.

node invokes the Node.js runtime.

src/index.js is the application entry point.

Understanding the command:

CMD ["node", "src/index.js"]

This is called the JSON or exec form of CMD.

It tells Docker to execute:

node src/index.js

when the container starts, unless a different command is supplied at runtime.

Why do we use it?

After creating a container from our image, Docker needs to know which process should run. Here, it starts our Node.js application.

Remember: CMD executes when the container starts, not when the Docker image is built.

6. EXPOSE 3000

Purpose: Documents the Application's Listening Port

EXPOSE indicates that the application inside the container is expected to listen on port 3000.

It acts as documentation about the network port used by the application.

It does not automatically publish the port to the host machine.

How do we access the application?

We need to publish the container port using the -p option:

docker run -d -p 3000:3000 my-node-app

Understanding port mapping:

Host Machine Port : Container Port
       3000       :       3000

Now the application can be accessed through:

http://localhost:3000

Remember: EXPOSE documents the port; -p publishes the port.

Complete Dockerfile Execution Flow

The Dockerfile follows this sequence:

FROM – Selects the Node.js base image.

WORKDIR – Creates/selects the application working directory.

COPY – Copies application files into the image.

RUN – Installs production dependencies.

CMD – Defines the application startup command.

EXPOSE – Documents the application's listening port.

Important Docker Concepts to Remember

Instruction

When Does It Execute?

Purpose

FROM

Image Build

Selects Base Image

WORKDIR

Image Build

Sets Working Directory

COPY

Image Build

Copies Files

RUN

Image Build

Executes Build Commands

CMD

Container Start

Defines Default Startup Command

EXPOSE

Image Build (Metadata)

Documents Container Port

Build and Run the Docker Image

Step 1 – Build the Image

docker build -t my-node-app .

Step 2 – Run the Container

docker run -d -p 3000:3000 --name node-container my-node-app

Step 3 – Check Running Containers

docker ps

Step 4 – View Container Logs

docker logs node-container

Step 5 – Stop the Container

docker stop node-container

Key Takeaways

Dockerfile contains instructions to build a Docker image.

FROM defines the base environment.

WORKDIR defines where application operations take place.

COPY transfers application files into the image.

RUN executes commands during image construction.

CMD defines the default process when the container starts.

EXPOSE documents the port used by the application.

Docker images are used to create and run containers.

Build → Ship → Run 🐳

> Note: The video uses `node:18-alpine` and `yarn install --production`. Node 18 reached end of life in April 2025 and the sample repo has since moved from yarn to npm, so the Dockerfile above uses a supported Node LTS image and `npm`. The rest of the steps are the same as in the video.

- Build the docker image using the application code and Dockerfile

```
docker build -t day02-todo .
```
- Verify the image has been created and stored locally using the below command:
```
docker images
```

- Create a public repository on hub.docker.com and push the image to remote repo
```
docker login
docker tag day02-todo:latest username/new-reponame:tagname
docker images
docker push username/new-reponame:tagname
```

- To pull the image to another environment , you can use below command
```
docker pull username/new-reponame:tagname
```

- To start the docker container, use below command

```
docker run -dp 3000:3000 username/new-reponame:tagname
```

- Verify your app. If you have followed the above steps correctly, your app should be listening on localhost:3000
- To enter(exec) into the container, use the below command

```
docker exec -it containername sh
or
docker exec -it containerid sh
```
- To view docker logs

```
docker logs containername
or
docker logs containerid
```



  
