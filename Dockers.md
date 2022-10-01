# Dockers

## Docker client

Docker client allows us to interact with docker engine, daemon running behind the scenes

- docker pull as the name suggests pulls images from the docker hub

```shell
docker pull <image name>
```

- below will run the image

- ```shell
  docker run <image name>
  ```

- lists images

```shell
docker images
```

- lists containers

```shell
docker ps
```

For example to pull an image called hello-world from docker hub

```bash
docker pull hello-world
```

To run it

```bash
docker run hello-world
```

To see all containers including the ones exited

```bash
docker ps -a
```

To remove the container

```bash
docker rm <container id>
```

To remove docker image

```bash
docker rmi <image id>
```

To start a docker nguni image you can specify container port and web server port within container

```bash
docker run -p 80:80 kitematic\hello-world-nginx
```

To stop the container

```bash
docker stop <container id>
```

To connect to a container and run commands

```
docker exec -it <container name> bash
```

## Layered filesystem

Docker image consists of layers of filesystems. Image layers are read only. We can launch multiple containers from the same image. Images can share layers among each other as well.

When we launch our container it will have its own R/W filesystem layer. But it will be removed when we remove the container. That's where volumes come in handy

### What is volume

Special type of directory associated with the container

Can be shared and reused among containers

Updates to an image won't affect data volume

Data volumes are persisted even after the container is removed

You can create a volume such as /var/www and when our container writes into it it actually writes to docker host mounted filesystem

You can think of /var/www as an alias to actual location where container writes into

You can run your image by specifying volume. In below example we run image called node, with external port 8080 and an internal port 3000 and specifying new volume. In other words below command will create a volume called /var/www on the container. Docker will automatically assign an actual location on its host. 

```bash
docker run -p 8080:3000 -v /var/www node
```

You can inspect your container to find out where the volume is actually mounted

```bash
docker inspect <container name>
```

If you want to customize volume and specify mounted location by yourself. Below command launched node image, by specifying mounted location equivalent to the current directory

```bash
docker run -p 8080:3000 -v $(pwd):/var/www node
```

Hook your source code to a docker

You can test this by installing express and express-generator globally

```bash
npm install express express-generator -g
```

Create express web server

```bash
express ExpressSite --hbs
```

Then run docker image node, by specifying mounted volume on docker host and specifyin working directory in which npm start should run on

```bash
docker run -p 8080:3000 -v $(pwd):/var/www -w "/var/www" node npm start
```



When you run docker by not explicitly specifying docker host mounted location, docker will automatically mount a location on docker host. When you are done with your container you might want to remove the container as well as the automatically allocated docker host mounted location

```bash
docker rm -v <container id|container name>
```

## Dockerfile

Docker images are created from Dockerfiles. You can think of them as instructions on how to create Docker image. We convert Dockerfiles to Docker images using ```docker build``` command

- Text file used to build Docker images
- Contains build instructions, such as how to include environment variables and source code
- Instructions create intermediate image that is cached to speed up future builds
- Used with ```docker build``` command

### Key Docker build instructions

- FROM : create image from another base image such as node, mongodb, postgresql
- LABEL : specify author
- RUN : run things like download resource from internet
- COPY : copy source code from dev to production
- ENTRYPOINT : what is the initial entry point that kicks off the container
- WORKDIR : where the container runs, like the folder that contains package.json for node application
- EXPOSE : expose a port that container will run internally with
- ENV : define environment variables
- VOLUME : define volumes, define how the container strores it on the docker host

Below is an example docker file in action

```dockerfile
FROM node:latest
LABEL author="samrullo"

ENV NODE_ENV=production
ENV PORT=3000

COPY . /var/www
WORKDIR /var/www
VOLUME ["/var/www"]
RUN npm install
EXPOSE $PORT
ENTRYPOINT ["npm","start"]
```

### Build image from dockerfile

You can build docker image running below command

Below command tags the new image with <username>/node and it provides the directory image has to be built from

```bash
docker build -t <username>/node .
```

By default Docker looks for Dockerfile to read instructions from

If you named your dockerfile something else like node.dockerfile then you have to use -f switch to specify it

```bash
docker build -f node.dockerfile -t <username>/node .
```

When you run docker build it starts creating multiple intermediate containers that will be cached behind the scenes. These are intermediate image layers that will speed up next builds

### Publishing docker image to docker HUB

After you create an account with Docker HUB, log in from your command line and push your image

```bash
docker login
docker push samrullo/node
```

## Linking containers by name

- Run a container with a name
- Link to running container by name
- Repeat for additional containers

For example we can start postgres database container with a name my-postgres

```bash
docker run -d --name my-postgres postgres
```

Then we can link web server container to above database container. In below we can optionally specify an alias after database container name separated by colon

```bash
docker run -p 8080:5000 -d --link my-postgres:postgres dawahlin/aspnetcore
```

In below example we will link node web server to mongodb database container

1. First we will start mongodb container from mongo image giving it a name

```bash
docker run -d --name my-mongodb mongo
```

2. Next we will build our node image and start it by linking to mongodb

```bash
docker build -f node.dockerfile -t samrullo/node .
docker run -d -p 8080:3000 --link my-mongodb:mongodb samrullo/node
```

3. Now our node webserver is connected to mongodb. To test it we will run a node command in our node container and insert some data to database

```bash
docker exec <container id> node dbSeeder.js
```

## Container Networks

When you have lots of containers running on docker host, you will start to want to split them into isolated groups. These isolated groups are called ```Bridge Networks```

- Create custom bridge network
- Run containers in the Network, i.e. when you run a container you also specify the bridge network in which it will run. It is possible for a container to run in multiple networks, which will allow it to talk to containers running in different networks.

1. Create custom bridge network

```bash
docker network create --driver bridge isolated_network
```

2. Run a container in the network

```bash
docker run -d --net=isolated_network --name mongodb mongo
```

Now a web container running in ```isolated_network``` can connect to mongodb by its name.

You can list bridge networks as below

```bash
docker network ls
```

You can also inspect a bridge network, which will print out information such as containers running in that bridge

```bash
docker network inspect isolated_network
```

## Docker compose

Docker compose manages your application lifecycle

- Start, stop and rebuild services, which are containers, but in the world of docker compose they are called services
- View the status of running services
- Stream the log output of running services
- Run a one-off command on a service

Suppose your whole application consists of nginx, node, mongodb, reds servers. Your nginx talks to multiple node servers, while each of those talk to mongodb and they also store some data in redis.

```docker-compose.yml``` file allows to define all those containers and also define their relationship as well.

### Docker compose workflow

1. Build Services : similar to building images
2. Start up services : similar to docker run container
3. When we are done tear down those services : stop the containers or remove them

docker-compose.yml file

As stated above, in docker-compose.yml we define various services, their configurations. On its own the file is not much useful. But we can use it with ```docker compose build``` to build images.

```yaml
version:"3.x"
services:
	#nginx
	#node
	#mongodb
```

Key service configuration options

- version : yaml file version, must be supported version such as 3.4
- build : like building images
- environment : setting environmetn variables used by a service
- image : just specify images as a service
- networks : specify networks in which our services will run in
- ports : expose ports
- volumes : define volumes

In below example we are defining node and mongodb services

1. node is the name of the service. We want to build it from a ```node.dockerfile``` and the image is build from the current folder, which is specified by ```context``` The service will run in the network called ```nodeapp-network```
2. mongodb service is not built, but rather runs from an image ```mongo``` and it will also run in the network ```nodeapp-network```
3. Finally we define networks. You can define multiple networks.

```yaml
version:"3.4"
service:
  node:
    build:
      context: .
      dockerfile: node.dockerfile
    networks:
      -nodeapp-network
  
  mongodb:
    image:mongo
    networks:
      -nodeapp-network
  
  networks:
    nodeapp-network:
      driver:bridge
```

Once we have our ```docker-compose.yml``` file we can run following commands from the directory where yaml file is located to manage our application lifecycle

1. We build our services by running

```bash
docker-compose build
```

2. Then you can start those services

```bash
docker-compose up
```

3. To stop the services

```bash
docker-compose down
```

To view logs

```bash
docker-compose logs
```

To view running containers

```bash
docker-compose ps
```

We can start, stop, remove certain containers

```bash
docker-compose start ...
docker-compose stop ...
docker-compose rm ...
```

**Docker compose build**

You can build all your services defined in ```docker-compose.yml```

```bash
docker-compose build
```

You can build specific services only

```bash
docker-compose build mongo
```

We can create and start services with docker-compose up. But we can also create and start specific services.

In below example, we start only ```node``` service and we are telling not to re-create or re-build any of its dependencies, such as mongodb

```bash
docker-compose up --no-deps node
```

Docker compose down stops the services and also removes containers

```bash
docker-compose down
```

If you want to stop services, and remove containers, images and volumes

```bash
docker-compose down --rmi all --volumes
```
# Create image from running container

- Image from container_name
  ```bash
  docker commit <container_name>
  ```

- Tag docker image
  ```bash
  docker tag <image_id>
  ```

- To remove a tag from an image, simply use ```docker rmi``` and specify image repository
  ```
  docker rmi <image repository name>:<image tag>
  ```


# Some issues

- When you run ```docker build``` without excluding ```node_modules``` you will face errors due to node modules having different binaries for different OS. Therefore, always indclude ```node_modules``` in your ```.dockerignore``` file
- node application container could connect to mongodb container only when I used --link before app. Meaning below command works

```bash
docker run --link my-mongodb --name whisper samrullo/node-whisper
```

But below doesn't

```bash
docker run samrullo/node-whisper --link my-mongodb --name whisper
```