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

#### Running Server Container
Whether you want to host a web application, an API, or a database, you want a container that listens for incoming network connections and is potentially long-lived. A server container is long-lived and listens for incoming network connections. Running a container as detached means that you immediately get your command-line back and the standard output from the container is not redirected to your command-line anymore.
```sh
docker run alpine ping www.docker.com
```
> The ping command doesn’t end since it keeps pinging the Docker server. That’s a long-lived container. You can detach from it using the Ctrl-C shortcut, and it keeps running in the background. However, it’s best to run it as detached from the beginning.
```sh
docker run -d alpine ping www.docker.com
```
> Note the addition of a -d switch. When doing so, the container starts, but we don’t see its output. Instead, the docker run command returns the ID of the container that was just created.

#### Interacting with the Running Container
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



