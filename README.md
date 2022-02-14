
# Docker Essentials
## Most used commands


|command  |explain  |shorthand  |
|--|--|--|
|`docker image ls`  | Lists all images |`docker images`  |
|`docker image rm <image>`  | Removes an image | `docker rmi` |
|`docker image pull <image>` |Pulls image from a docker registry  |`docker pull`  |
|`docker container ls -a`  |Lists all containers  | `docker ps -a` |
|`docker container run <image>`  |Runs a container from an image  |`docker run`  |
|`docker container rm <container>`  |Removes a container  | `docker rm` |
|`docker container stop <container>`  | Stops a container |`docker stop`  |
|`docker container exec <container>`  | Executes a command inside the container |  `docker exec`|



## What is docker?
"Docker is a set of platform as a service (PaaS) products that use OS-level virtualization to deliver software in packages called containers." - [from Wikipedia](https://en.wikipedia.org/wiki/Docker_(software)).

1.  Docker is a set of tools to deliver software in containers.
2.  Containers are packages of software.

![](https://devopswithdocker.com/static/6ec65bc81fa5c52af7e93008d5196f98/4597d/container.png)

## Docker vs VM
![docker explained 3](https://devopswithdocker.com/static/774514d8c4e314ac6483e9a50697af13/7fee5/docker-explained-3.png)

## Container vs Image vs Dockerfile
![Build a Docker Image just like how you would configure a VM | by Nilesh  Jayanandana | Platformer — A WSO2 Company | Medium](https://miro.medium.com/max/1400/1*p8k1b2DZTQEW_yf0hYniXw.png)

- Dockerfile -> Recipe
- Docker Image -> Pre-cooked frozen treat (difficult to make)
- Docker Container -> Delicious treat (Easy to warm up)

## Running Containers
`docker container run <image>`

`docker container run hello-world`

## Docker Image
A Docker image is a file. An image never changes; you can not edit an existing file. Creating a new image happens by starting from a base image and adding new **layers** to it.

**list all images**

` docker image ls`

**delete image**

`docker image rm <image-name>`

**download image without running**

`docker image pull <image-name>`

This image file is built from an instructional file named **Dockerfile** that is parsed when you run `docker image build`.
## Dockerfile

```dockerfile
FROM <image>:<tag>

RUN <install some dependencies>

CMD <command that is executed on `docker container run`>
```

## Container
Containers only contain that which is required to execute an application; and you can start, stop and interact with them. They are  **isolated**  environments in the host machine with the ability to interact with each other and the host machine itself via defined methods (TCP/UDP).

List all your running containers with  `docker container ls`

**List all containers** 

`docker container ls -a`

**delete container**

`docker container rm <container-name>`

`docker container rm <container-id>`

`docker container rm 3d4bab29dd67`

`docker container rm 3d`

`docker container rm id1 id2 id3`

**delete all stopped containers**

`docker container prune`

**run container in the background, *detached*** 

`docker container run -d <image-name>`

`docker container run -d nginx`

**stop running container**

`docker container stop <container-name or container-id>`

**forcefully delete container**

`docker container rm --force <container>`

**interact with container**

`docker run -it ubuntu`

- `-i` flag will instruct to pass the STDIN to the container
- `-t` will create a tty

**enter container by starting new process in it (container must be running)**

`docker exec -it <container> bash`

**automatically remove container after it has exited**

`docker run -d --rm -it <image>`

**attach to a running container**

`docker attach <container>`

**start container and give command to container**

`docker run -d -it --name looper ubuntu sh -c 'while true; do date; sleep 1; done'`

-   The first part,  `docker run -d`. Should be familiar by now, run container detached.
    
-   Followed by  `-it`  is short for  `-i`  and  `-t`. Also familiar,  `-it`  allows you to interact with the container by using the command line.
    
-   Because we ran the container with  `--name looper`, we can now reference it easily.
    
-   The image is  `ubuntu`  and what follows it is the command given to the container.

## Building a Dockerfile
Dockerfile is simply a file that contains the build instructions for an image. You define what should be included in the image with different instructions.

```dockerfile
# Start from the alpine image that is smaller but no fancy tools
FROM alpine:3.13

# Use /usr/src/app as our workdir. The following instructions will be executed in this location.
WORKDIR /usr/src/app

# Copy the hello.sh file from this location to /usr/src/app/ creating /usr/src/app/hello.sh
COPY hello.sh .

# Alternatively, if we skipped chmod earlier, we can add execution permissions during the build.
RUN chmod +x hello.sh

# When running docker run the command will be ./hello.sh
CMD ./hello.sh
```
By default `docker build` will look for a file named Dockerfile. Now we can run `docker build` with instructions where to build (`.`) and give it a name (`-t <name>`):

```console
docker build . -t hello-docker
```
Now executing the application is as simple as running `docker run hello-docker`

**CMD**

This command will be executed when the container is run using `docker run`


**ENTRYPOINT**

**Any argument given to `docker run` replaces the command or `CMD`.** We need a way to have something before the command. Luckily we have a way to do this: we can use `ENTRYPOINT` to define the main executable and then docker will combine our run arguments for it.

```dockerfile
FROM ubuntu:18.04

WORKDIR /mydir

RUN apt-get update && apt-get install -y curl python
RUN curl -L https://yt-dl.org/downloads/latest/youtube-dl -o /usr/local/bin/youtube-dl
RUN chmod a+x /usr/local/bin/youtube-dl

ENV LC_ALL=C.UTF-8

# Replacing CMD with ENTRYPOINT
ENTRYPOINT ["/usr/local/bin/youtube-dl"]
```
And now it works like it should:

```console
$ docker build -t youtube-dl .
$ docker run youtube-dl https://imgur.com/JY5tHqr

  [Imgur] JY5tHqr: Downloading webpage
  [download] Destination: Imgur-JY5tHqr.mp4
  [download] 100% of 190.20KiB in 00:0044MiB/s ETA 00:000
```

By default entrypoint is set as `/bin/sh` and this is passed if no entrypoint is set.
