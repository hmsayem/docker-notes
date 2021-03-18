# Docker
To get help for Docker commands from the command-line:
```sh
docker run --help
```
##### Commands for container management
- `docker ps` lists the containers that are still running. Add the `-a` switch to see containers that have stopped
- `docker logs` retrieves the logs of a container, even when it has stopped
- `docker inspect` gets detailed information about a running or stopped container
- `docker stop` deletes a container that is still running
- `docker rm` deletes a container

##### To remove all containers:
```sh
docker container prune -f
```
> This is the equivalent of running one `docker rm` command for each stopped container. The -f switch is an implicit confirmation to proceed and delete all stopped containers right away, instead of asking to confirm that operation.

### Running Server Container
Whether you want to host a web application, an API, or a database, you want a container that listens for incoming network connections and is potentially long-lived. A server container is long-lived and listens for incoming network connections. Running a container as detached means that you immediately get your command-line back and the standard output from the container is not redirected to your command-line anymore.
```sh
docker run alpine ping www.docker.com
```
> The ping command doesn’t end since it keeps pinging the Docker server. That’s a long-lived container. You can detach from it using the Ctrl-C shortcut, and it keeps running in the background. However, it’s best to run it as detached from the beginning.
```sh
docker run -d alpine ping www.docker.com
```
> Note the addition of a -d switch. When doing so, the container starts, but we don’t see its output. Instead, the docker run command returns the ID of the container that was just created.

### Interacting with the Running Container
To see the standard output of the docker:
```sh
docker logs <id>
```
>The above command prints the whole standard output of the container from its beginning, which may be lengthy. `–from`, `–until`, or `–tail` `switches` are used to get a portion of the output. 

Most recent 10 seconds of logs for the running container:
```sh
docker logs --since 10s <id>
```
Stopping and cleaning up the runnning container:

```sh
docker stop <id>
docker rm <id>
```
#### Listening for Incoming Network Connections 
The -p switch takes two parameters; the incoming port you want to open on the host machine, and the port to which it should be mapped inside the container. 
```sh
docker run -d -p 8085:80 nginx
```
> The machine listens for incoming connections on port 8085 and route them to port 80 inside a container that runs NGINX.

### Using Volumes
Volumes are used to store files in place where they are persisted. A directory inside the container can be mapped to a persistent storage using a volume. Persistent storages are managed through drivers, and they depend on the actual Docker host. They may be an Azure File Storage on Azure or Amazon S3 on AWS. Actual directories on the host system can be mapped using `-v` switch.
```
docker run -v /your/dir:/var/lib/mysql -d mysql:5.7
```
> It will ensure that any data written to the `/var/lib/mysql` directory inside the container is actually written to the `/your/dir` directory on the host system. This ensures that the data is not lost when the container is restarted.

### Docker Images
Command to list the local images:
```
docker image ls
```
The docker run command downloads images automatically when missing. The `docker pull` command forces an image to download, whether it is already present or not.

#### Create an Image
A Docker image is created using the `docker build` command and a `Dockerfile` file. The Dockerfile file contains instructions on how the image should be built. A `Dockerfile` always begins with a FROM instruction because every image is based on another base image.  Here’s a `Dockerfile` that creates a Debian Linux-based image and instructs it to greet our users when a container spawns:
```
FROM debian:8
CMD ["echo", "Hello world"]
```

The `docker build` command is run in order to create an image from my `Dockerfile`.
```
docker build -t hello .
```
>The -t switch is used in front of the desired image to give it a name. An image can be created without a name, it would have an auto-generated unique ID, so it is an optional parameter on the docker build command.

#### Creating an Image Including Files
The Docker Hub contains an NGINX image where NGINX has already been installed with a configuration that serves files found in the /usr/share/nginx/html directory.
```
FROM nginx:1.15
COPY index.html /usr/share/nginx/html
```
```
docker build -t webserver .
docker run --rm -it -p 8082:80 webserver`
```
> - The -it switch allows user to stop the container using Ctrl-C from the command-line
> - The –rm switch ensures that the container is deleted once it has stopped

To see the images available locally on my computer by:
```
docker image ls
```
To remove an image:
```
docker rmi <id>
docker rmi <repository> : <tag>
```

### Tags

In order to apply a tag:
```
docker build -t <name>:<tag> .
```

### Environment Variables

#### Reading a Value

If an environment variable `name` is set, it can be accessed with:

| Technology  | Acess |
| ------------- | ------------- |
| Linux shell  | $name  |
|Go|os.Getenv("FOO")|
|   Java | System.getenv(“name”)  |
|Python|os.environ.get('name')|


#### Providing a Value
In order to provide an environment variable’s value at runtime, `-e name=value` parameter is used on the docker run command.


#### Default Value
A default value can be provided for an environment variable, in case it isn't provided when a container is created. This is done in the `Dockerfile`, using `ENV` instruction. 

```
ENV name = <value>
```
#### Example:

Creating an image that can ping any given site using a Linux shell script. 

>ping.sh

```
#!/bin/sh

echo "Pinging $host..."
ping -c 5 $host
```

>Dockerfile
```
FROM debian:8

ENV host=www.google.com

COPY ping.sh .

CMD ["sh", "ping.sh"]
```
>Commands
```
docker build -t pinger .
docker run --rm pinger
docker run --rm -e host=www.bing.com pinger
```
