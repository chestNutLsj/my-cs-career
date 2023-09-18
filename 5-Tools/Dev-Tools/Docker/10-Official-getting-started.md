## Docker components

Docker consists of multiple parts:

- The Docker daemon (sometimes also called the Docker Engine), which is a process which runs as docker. service. It serves the Docker API and manages Docker containers.
- The docker CLI command, which allows users to interact with the Docker API via the command line and control the Docker daemon.
- Docker containers, which are namespaced processes that are started and managed by the Docker daemon as requested through the Docker API.

## Running docker CLI
Typically , users use Docker by running `docker` CLI commands, which in turn request the Docker daemon to perform actions which in turn result in management of Docker containers. 

Understanding the relationship between the client (`docker`), server (`docker.service`) and containers is important to successfully administering Docker.

Note that ==if the Docker daemon stops or restarts, all currently running Docker containers are also stopped or restarted==.

Also note that it is possible to send requests to the Docker API and control the Docker daemon without the use of the `docker` CLI command. See the [Develop with Docker Engine API | Docker Docs](https://docs.docker.com/engine/api/) for more information.

## 1. Overview

This guide contains step-by-step instructions on how to get started with Docker. This guide shows you how to:
* Build and run an image as a container.
* Share images using Docker Hub.
* Deploy Docker applications using multiple containers with a database.
* Run applications using Docker Compose.

### What is a container?

A container is a sandboxed process running on a host machine that is isolated from all other processes running on that host machine. That isolation leverages [kernel namespaces and cgroupsopen_in_new](https://medium.com/@saschagrunert/demystifying-containers-part-i-kernel-space-2c53d6979504), features that have been in Linux for a long time. Docker makes these capabilities approachable and easy to use. 

To summarize, a container:
* Is a runnable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI.
* Can be run on local machines, virtual machines, or deployed to the cloud.
* Is portable (and can be run on any OS).
* Is isolated from other containers and runs its own software, binaries, configurations, etc.

If you're familiar with `chroot`, then think of a container as an extended version of `chroot`. The filesystem comes from the image. However, a container adds additional isolation not available when using chroot.

### What is an image?

A running container uses an isolated filesystem. This isolated filesystem is provided by an image, and the image must contain everything needed to run an application - all dependencies, configurations, scripts, binaries, etc. The image also contains other configurations for the container, such as environment variables, a default command to run, and other metadata.

## 2. Containerize an application

For the rest of this guide, you'll be working with a simple todo list manager that runs on `Node.js`. If you're not familiar with `Node.js`, don't worry. This guide doesn't require any prior experience with JavaScript.

### Prerequisites

* You have installed the latest version of [Docker Desktop](https://docs.docker.com/get-docker/).
* You have installed a [Git clientopen_in_new](https://git-scm.com/downloads).
* You have an IDE or a text editor to edit files. Docker recommends using [Visual Studio Codeopen_in_new](https://code.visualstudio.com/).

### Get the app

Before you can run the application, you need to get the application source code onto your machine.

1. Clone the [getting-started-app repositoryopen_in_new](https://github.com/docker/getting-started-app/tree/main) using the following command:

```
$ git clone https://github.com/docker/getting-started-app.git
```

2.  View the contents of the cloned repository. You should see the following files and sub-directories.

```
├── getting-started-app/
│ ├── package.json
│ ├── README.md
│ ├── spec/
│ ├── src/
│ └── yarn.lock
```

### Build the app's image

==To build the image, you'll need to use a Dockerfile. ==

A Dockerfile is simply a text-based file with no file extension that contains a script of instructions. Docker uses this script to build a container image.

1. In the `getting-started-app` directory, the same location as the `package.json` file, create a file named `Dockerfile`. You can use the following commands to create a Dockerfile based on your operating system.
```shell
# In the terminal, run the following commands.

# Make sure you're in the `getting-started-app` directory.
cd /path/to/getting-started-app

# Create an empty file named `Dockerfile`
touch Dockerfile
```


2.  Using a text editor or code editor, add the following contents to the Dockerfile:
```
# syntax=docker/dockerfile:1

FROM node:18-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```

3.  Build the image using the following commands:
```shell
# Build the image
docker build -t getting-started .
```

The `docker build` command uses the Dockerfile to build a new image. ==You might have noticed that Docker downloaded a lot of "layers". This is because you instructed the builder that you wanted to start from the `node:18-alpine` image.== But, since you didn't have that on your machine, Docker needed to download the image.

After Docker downloaded the image, the instructions from the Dockerfile (which is the `RUN` instruction ) copied in your application and used ` yarn ` to install your application's dependencies. ==The ` CMD ` directive specifies the default command to run when starting a container from this image==.

Finally, the `-t` flag tags your image. Think of this as a human-readable name for the final image. Since you named the image `getting-started`, you can refer to that image when you run a container.

The `.` at the end of the `docker build` command tells Docker that it should look for the `Dockerfile` in the current directory.

```
# The correct output
❯ docker build -t getting-started .  
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.  
           Install the buildx component to build images with BuildKit:  
           https://docs.docker.com/go/buildx/  
  
Sending build context to Docker daemon  6.536MB  
Step 1/6 : FROM node:18-alpine  
---> 50c7e33a9de1  
Step 2/6 : WORKDIR /app  
---> Using cache  
---> 4dc6c6fec12b  
Step 3/6 : COPY . .  
---> Using cache  
---> e7c7c17d6e90  
Step 4/6 : RUN yarn install --production  
---> Running in 67fb6a84624a  
yarn install v1.22.19  
[1/4] Resolving packages...  
[2/4] Fetching packages...  
[3/4] Linking dependencies...  
[4/4] Building fresh packages...  
Done in 80.28s.  
Removing intermediate container 67fb6a84624a  
---> eb3f2fc36464  
Step 5/6 : CMD ["node", "src/index.js"]  
---> Running in 80daefc1d806  
Removing intermediate container 80daefc1d806  
---> 14a7714a60e3  
Step 6/6 : EXPOSE 3000  
---> Running in fdb835fc2e6d  
Removing intermediate container fdb835fc2e6d  
---> d2d0b21440d8  
Successfully built d2d0b21440d8  
Successfully tagged getting-started:latest
```

### Start an app container

Now that you have an image, you can run the application in a container using the `docker run` command.

1. Run your container using the `docker run` command and specify the name of the image you just created:

```shell
docker run -dp 127.0.0.1:3000:3000 getting-started

# the correct output maybe
60944f2b639a6ce33f097ea26a4363b3139cebd9e010c08c10b183075de0106b
```

The `-d` flag (short for `--detach`) runs the container in the background. 
The `-p` flag (short for `--publish`) ==creates a port mapping between the host and the container==. 

The `-p` flag takes a string value in the format of `HOST:CONTAINER`, where `HOST` is the address on the host, and `CONTAINER` is the port on the container. The command publishes the container's port 3000 to `127.0.0.1:3000` (`localhost:3000`) on the host. 

Without the port mapping, you wouldn't be able to access the application from the host.

2. After a few seconds, open your web browser to [http://localhost:3000open](http://localhost:3000/). You should see your app.

![[10-Usage-todo-app.png]]

3. Add an item or two and see that it works as you expect. You can mark items as complete and remove them. ==Your frontend is successfully storing items in the backend==.


At this point, you have a running todo list manager with a few items.

### See container's process
If you take a quick look at your containers, you should see at least one container running that's using the `getting-started` image and on port `3000`. To see your containers, you can use the CLI or Docker Desktop's graphical interface.

Run the following `docker ps` command in a terminal to list your containers.

```shell
docker ps
```

Output similar to the following should appear.

```
❯ docker ps  
CONTAINER ID   IMAGE             COMMAND                   CREATED         STATUS         PORTS          
             NAMES  
60944f2b639a   getting-started   "docker-entrypoint.s…"   3 minutes ago   Up 3 minutes   127.0.0.1:3000  
->3000/tcp   awesome_cartwright
```

### Related reference
* [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
* [docker CLI reference](https://docs.docker.com/engine/reference/commandline/cli/)
* [Build with Docker guide](https://docs.docker.com/build/guide/)

## Update the application

### Update the source code
In the following steps, you'll change the "empty text" when you don't have any todo list items to "You have no todo items yet! Add one above!"

1. In the `src/static/js/app.js` file, update line 56 to use the new empty text.

```diff
- <p className="text-center">No items yet! Add one above!</p>
+ <p className="text-center">You have no todo items yet! Add one above!</p>
```

2. Build your updated version of the image, using the `docker build` command .

```shell
docker build -t getting-started .

# The correct output as following:
❯ docker build -t getting-started .  
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.  
           Install the buildx component to build images with BuildKit:  
           https://docs.docker.com/go/buildx/  
  
Sending build context to Docker daemon  6.536MB  
Step 1/6 : FROM node:18-alpine  
---> 50c7e33a9de1  
Step 2/6 : WORKDIR /app  
---> Using cache  
---> 4dc6c6fec12b  
Step 3/6 : COPY . .  
---> 72002222baeb  
Step 4/6 : RUN yarn install --production  
---> Running in f0874a8e4016  
yarn install v1.22.19  
[1/4] Resolving packages...  
[2/4] Fetching packages...  
[3/4] Linking dependencies...  
[4/4] Building fresh packages...  
Done in 90.58s.  
Removing intermediate container f0874a8e4016  
---> 54276bc8efbb  
Step 5/6 : CMD ["node", "src/index.js"]  
---> Running in 2c8eafee3f04  
Removing intermediate container 2c8eafee3f04  
---> 992012734e8e  
Step 6/6 : EXPOSE 3000  
---> Running in e4425a8dfb91  
Removing intermediate container e4425a8dfb91  
---> adb88ebe06a8  
Successfully built adb88ebe06a8  
Successfully tagged getting-started:latest
```

3. Start a new container using the updated code.

```shell
docker run -dp 127.0.0.1:3000:3000 getting-started
```


You probably saw an error like this:

```console
docker: Error response from daemon: driver failed programming external connectivity on endpoint laughing_burnell 
(bb242b2ca4d67eba76e79474fb36bb5125708ebdabd7f45c8eaf16caaabde9dd): Bind for 127.0.0.1:3000 failed: port is already allocated.
```

The error occurred because you aren't able to start the new container while your old container is still running. The reason is that the old container is already using the host's port 3000 and only one process on the machine (containers included) can listen to a specific port. To fix this, you need to remove the old container.

### Remove the old container

==To remove a container, you first need to stop it. ==Once it has stopped, you can remove it. You can remove the old container using the CLI or Docker Desktop's graphical interface. Choose the option that you're most comfortable with.
#### Remove a container using the CLI

1. Get the ID of the container by using the `docker ps` command.

```console
$ docker ps
```

2. Use the `docker stop` command to stop the container. Replace `<the-container-id>` with the ID from `docker ps`. 

```console
$ docker stop <the-container-id>
```

3. Once the container has stopped, you can remove it by using the `docker rm` command. 

```console
$ docker rm <the-container-id>
```


> **Note**
> 
> You can stop and remove a container in a single command by adding the `force` flag to the `docker rm` command. For example: `docker rm -f <the-container-id>`
#### Start the updated app container

1. Now, start your updated app using the command.`docker run`

```console
docker run -dp 127.0.0.1:3000:3000 getting-started

# A new output
c9865e68ee361fe76c57b0df66dbdb243eb5714bac6a2ed38038ffa58c1d9f52

```

2. Refresh your browser on [http://localhost:3000open](http://localhost:3000/) and you should see your updated help text.

### Related information:
- [docker CLI reference](https://docs.docker.com/engine/reference/commandline/cli/)

## Share the application

Now that you've built an image, you can share it. To share Docker images, you have to use a Docker registry. The default registry is Docker Hub and is where all of the images you've used have come from.

> **Docker ID**
> 
> A Docker ID lets you access Docker Hub, which is the world's largest library and community for container images. Create a [Docker IDopen](https://hub.docker.com/signup) for free if you don't have one.

### Create a repository

To push an image, you first need to create a repository on Docker Hub.

1. [Sign up](https://www.docker.com/pricing?utm_source=docker&utm_medium=webreferral&utm_campaign=docs_driven_upgrade) or Sign in to [Docker Hub](https://hub.docker.com/).
2. Select the **Create Repository** button.
3. For the repository name, use `getting-started`. Make sure the **Visibility** is **Public**.
4. Select **Create**.

In the following image, you can see an example Docker command from Docker Hub. This command will push to this repository.

![[10-Official-getting-started-create-repo.png]]

### Push the image

1. In the command line, run the `docker push` command that you see on Docker Hub. Note that your command will have your Docker ID, not "docker".

```
❯ docker push chestnutlsj/getting-started:getting-started  
The push refers to repository [docker.io/chestnutlsj/getting-started]  
An image does not exist locally with the tag: chestnutlsj/getting-started
```

==Why did it fail?== The push command was looking for an image named `chestnutlsj/getting-started`, but didn't find one. If you run `docker image ls`, you won't see one either.

To fix this, you need to tag your existing image you've built to give it another name.

2. Sign in to Docker Hub using the command `docker login -u YOUR-USER-NAME`.
```
# correct output:
❯ docker login -u chestnutlsj  
Password:    
WARNING! Your password will be stored unencrypted in /home/senjl/.docker/config.json.  
Configure a credential helper to remove this warning. See  
https://docs.docker.com/engine/reference/commandline/login/#credentials-store  
  
Login Succeeded
```

3. Use the `docker tag` command to give the `getting-started` image a new name. Replace `YOUR-USER-NAME` with your Docker ID.

```
docker tag getting-started:latest chestnutlsj/getting-started:hellodocker
```

4. Now run the `docker push` command again. If you're copying the value from Docker Hub, you can drop the `tagname` part, as you didn't add a tag to the image name. If you don't specify a tag, Docker uses a tag called `latest`.

```
❯ docker push chestnutlsj/getting-started:hellodocker  
The push refers to repository [docker.io/chestnutlsj/getting-started]  
62aeed21ca62: Pushed    
d851decfeff3: Pushed    
e1616fdfe6cb: Pushed    
498c77a984f9: Mounted from library/node    
69b50a78845f: Mounted from library/node    
ea2a97a3209c: Mounted from library/node    
4693057ce236: Mounted from library/node    
hellodocker: digest: sha256:dec513ddea8f7df4d3d3784d50a8923d0d96a35e54be002187f71a864cf93b16 size: 1787
```

### Run the image on a new instance

Now that your image has been built and pushed into a registry, try running your app on a brand new instance that has never seen this container image. To do this, you will use Play with Docker.

> [! note] Play with Docker.
> 
> Play with Docker uses the amd64 platform. If you are using an ARM based Mac with Apple Silicon, you will need to rebuild the image to be compatible with Play with Docker and push the new image to your repository.
> 
> To build an image for the amd64 platform, use the `--platform` flag.
> 
> ```
> $ docker build --platform linux/amd64 -t YOUR-USER-NAME/getting-started .
> ```
> 
> Docker buildx also supports building multi-platform images. To learn more, see [Multi-platform images](https://docs.docker.com/build/building/multi-platform/).

1. Open your browser to [Play with Dockeropen_in_new](https://labs.play-with-docker.com/).

2. Select **Login** and then select **docker** from the drop-down list.

3. Sign in with your Docker Hub account and then select **Start**.

4. Select the **ADD NEW INSTANCE** option on the left side bar. If you don't see it, make your browser a little wider. After a few seconds, a terminal window opens in your browser.

![[10-Official-getting-started-play-docker.png]]

5. In the terminal, start your freshly pushed app.

```
docker run -dp 0.0.0.0:3000:3000 YOUR-USER-NAME/getting-started
```

You should see the image get pulled down and eventually start up.

![[10-Official-getting-started-run-in-playdocker.png]]

> **Tip**
> 
> You may have noticed that this command binds the port mapping to a different IP address. Previous `docker run` commands published ports to `127.0.0.1:3000` on the host. This time, you're using `0.0.0.0`.
> 
> ==Binding to `127.0.0.1` only exposes a container's ports to the loopback interface. Binding to `0.0.0.0`, however, exposes the container's port on all interfaces of the host, making it available to the outside world.==
> 
> For more information about how port mapping works, see [Networking](https://docs.docker.com/network/#published-ports).

6. Select the 3000 badge when it appears.

If the 3000 badge doesn't appear, you can select **Open Port** and specify `3000`.

### Related information:

* [docker CLI reference](https://docs.docker.com/engine/reference/commandline/cli/)
* [Multi-platform images](https://docs.docker.com/build/building/multi-platform/)
* [Docker Hub overview](https://docs.docker.com/docker-hub/)

## Persist the DB

In case you didn't notice, your todo list is empty every single time you launch the container. Why is this? In this part, you'll dive into how the container is working.

### The container's filesystem

When a container runs, it uses the various layers from an image for its filesystem. ==Each container also gets its own "scratch space" to create/update/remove files.== Any changes won't be seen in another container, even if they're using the same image.

#### See this in practice

To see this in action, you're going to start two containers and create a file in each. What you'll see is that the files created in one container aren't available in another.

> **Note**
> 
> If you use Windows and want to use Git Bash to run Docker commands, see [Working with Git Bash](https://docs.docker.com/desktop/troubleshoot/topics/#working-with-git-bash) for syntax differences.

1. Start an `ubuntu` container that will create a file named `/data.txt` with a random number between 1 and 10000.

```shell
docker run -d ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"

# correct output
Unable to find image 'ubuntu:latest' locally  
latest: Pulling from library/ubuntu  
445a6a12be2b: Pull complete    
Digest: sha256:aabed3296a3d45cede1dc866a24476c4d7e093aa806263c27ddaadbdce3c1054  
Status: Downloaded newer image for ubuntu:latest  
afa5daf207cfaad42202af138a4e8800741a87520b87397a8ab18088e740d951
```

In case you're curious about the command, you're starting a bash shell and invoking two commands (why you have the `&&`). The first portion picks a single random number and writes it to `/data.txt`. The second command is simply watching a file to keep the container running.

2. Validate that you can see the output by accessing the terminal in the container. To do so, you can use the CLI or Docker Desktop's graphical interface.

On the command line, use the `docker exec` command to access the container. You need to get the container's ID (use `docker ps` to get it). In your Mac or Linux terminal, or in Windows Command Prompt or PowerShell, get the content with the following command.

```
docker exec <containerID> cat /data.txt  

# a possible output
5351
```

You should see a random number.

3. Now, start another `ubuntu` container (the same image) and you'll see you don't have the same file. In your Mac or Linux terminal, or in Windows Command Prompt or PowerShell, get the content with the following command.

```
docker run -it ubuntu ls /

# correct output:
bin   dev  home  lib32  libx32  mnt  proc  run   srv  tmp  var  
boot  etc  lib   lib64  media   opt  root  sbin  sys  usr
```

In this case the command lists the files in the root directory of the container. Look, there's no `data.txt` file there! That's because it was written to the scratch space for only the first container.

4.  Go ahead and remove the first container using the `docker rm -f <container-id>` command.

### Container volumes

With the previous experiment, you saw that each container starts from the image definition each time it starts. While containers can create, update, and delete files, those changes are lost when you remove the container and Docker isolates all changes to that container. With volumes, you can change all of this.

[Volumes](https://docs.docker.com/storage/volumes/) provide the ability to connect specific filesystem paths of the container back to the host machine. If you mount a directory in the container, changes in that directory are also seen on the host machine. If you mount that same directory across container restarts, you'd see the same files.

There are two main types of volumes. You'll eventually use both, but you'll start with volume mounts.

### Persist the todo data
By default, the todo app stores its data in a SQLite database at `/etc/todos/todo.db` in the container's filesystem. If you're not familiar with SQLite, no worries! It's simply a relational database that stores all the data in a single file. While this isn't the best for large-scale applications, it works for small demos. You'll learn how to switch this to a different database engine later.

With the database being a single file, if you can persist that file on the host and make it available to the next container, it should be able to pick up where the last one left off. 

==By creating a volume and attaching (often called "mounting") it to the directory where you stored the data, you can persist the data.== As your container writes to the `todo.db` file, it will persist the data to the host in the volume.

As mentioned, you're going to use a volume mount. Think of a volume mount as an opaque bucket of data. Docker fully manages the volume, including the storage location on disk. You only need to remember the name of the volume.

#### Create a volume and start the container
You can create the volume and start the container using the CLI or Docker Desktop's graphical interface.

1. Create a volume by using the `docker volume create` command.

```
$ docker run -it ubuntu ls /
```

2.  Stop and remove the todo app container once again with `docker rm -f <id>`, as it is still running without using the persistent volume.

3.  Start the todo app container, but add the `--mount` option to specify a volume mount. Give the volume a name, and mount it to `/etc/todos` in the container, which captures all files created at the path. In your Mac or Linux terminal, or in Windows Command Prompt or PowerShell, run the following command:

```
$ docker volume create todo-db
```


#### Verify that the data persists

1.  Once the container starts up, open the app and add a few items to your todo list.
    
    ![](https://docs.docker.com/get-started/images/items-added.png)
    
2.  Stop and remove the container for the todo app. Use Docker Desktop or `docker ps` to get the ID and then `docker rm -f <id>` to remove it.
    
3.  Start a new container using the previous steps.
    
4.  Open the app. You should see your items still in your list.
    
5.  Go ahead and remove the container when you're done checking out your list.
    

You've now learned how to persist data.

### Dive into the volume

A lot of people frequently ask "Where is Docker storing my data when I use a volume?" If you want to know, you can use the `docker volume inspect` command.

```
$ docker volume inspect todo-db
[
    {
        "CreatedAt": "2019-09-26T02:18:36Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/todo-db/_data",
        "Name": "todo-db",
        "Options": {},
        "Scope": "local"
    }
]
```

The `Mountpoint` is the actual location of the data on the disk. Note that on most machines, you will need to have root access to access this directory from the host.

### Related information:

* [docker CLI reference](https://docs.docker.com/engine/reference/commandline/cli/)
* [Volumes](https://docs.docker.com/storage/volumes/)

