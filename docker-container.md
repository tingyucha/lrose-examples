# The Lrose-Blaze Docker image

## What is it?

Lrose-Blaze allows you to run Lrose applications without having to compile and install lrose binaries and libraries. 

It is a Ubuntu 14.04 image, with lrose-core installed in 
/usr/local/lrose.

It has one user named **lrose** so if you don't want to run commands as root, you can specify **-u lrose** when you `docker run` or `docker exec`

## Installing and running

### Getting the lrose-blaze image

* From an official docker source (will we have that option?)

   `docker pull lrose-blaze`

* Download the image from (...) and install it

   `docker import lroze-blaze`

Verify it is there with `docker images`

```
$ docker images
REPOSITORY          TAG                 IMAGE ID     
lrose-blaze         latest              0a2ce9c9ae0f 
ubuntu_apache2      latest              4b5eefbe9c6c 
ncareol/soloii      latest              baa1ee4c3541
```

### Starting an lrose-blaze container

Here you have a few scenarios to choose from:

1. One time command run

	```
	docker run --rm -it ... lrose-blaze \
	           /usr/local/lrose/bin/RadxCheck ...
	```
	
2. Keep the container running and execute multiple commands

  First, start the contanier in *detached* mode (the -d option).
  Give the container a name (the -n option) so that it is easier to
  reference than the container id you get from `docker ps`

   `docker run -d ... lrose-blaze -n my_container`
   
  Then you can *exec* commands in this container.
   
   ```
   docker exec -it my_container ... \
               /usr/local/lrose/bin/RadxCheck ...
   ```
   
   
3. Get a interactive shell in the container and run multiple commands from inside the container

   `docker run -it ... /bin/bash`
   
#### Mapping volumes (directories, folders)

You want to give commands in the container access to your data files. You do that by mapping **volumes** when starting your container with the run command.

The general format for the volume option is

   `-v machine_folder:container_folder`
   
You can have multiple **-v** options if you want access to multiple folders.

So for example if your data resides in **$HOME/mydata**, and you want to run run lrose commands that will write the output files to 
**$HOME/myoutput**, you could use the following options

   `-v $HOME/mydata:/data -v $HOME/myoutput:output`
   
Note that you need to use **absolute paths**. So in this example, there will be **/mydata** and **/myoutput** directories accessible from inside your container. 

If the options you give the command running inside the container direct it to find data from **/data** and to write output to **/output**, you should see the output in **$HOME/mydata** from outside the container once the command is done.

Also note that volume mapping must be done at the time you **run** a container. You can't add volume mapping when you **exec** a command on a running container. (TODO check if this is true)

#### Other useful options

talk about DISPLAY, with example for both Mac and Linux

talk about environment variables

... what else?

## Some useful commands to manage containers and images

### List images available on your machine

`docker images`

This will list all images available to run, including their name and unique id.

### Stopping an lrose-blaze container

* If you were in an interactive shell, just `exit` That should get you out and stop the container

* If you want to stop a running container from outside of it

`docker stop ...`

Where ... is either the container name, or its unique id.

### List running containers

`docker ps`

This will list all running containers. You can use this command to get a container unique id.

### List non-running containers

`docker ps -a`

This will show when a container exited. 

Unless you provided the `-rm` option to the ***run*** command, a container that exited is still available on your machine. You could restart it with the `docker restart` command.

If you make any change inside the container, for example install more packages, add users, ..., these changes will persist if you restart the container. But once you remove it, all is lost.

### Permanently saving changes made to a container

You can preserve changes you have made by *comitting* a container to a new image. This can be very useful to customize your environment.

**Note** It is much safer to commit a non-running container. So stop the container first `docker stop ...` first, before using `docker commit ...`

This is outside the scope of this document, but you could for example install additional packages (ubuntu is debian based, so use the apt-get install command to do so).
You could add users, and customize their startup files (.bashrc, .chsrc, ...) to add aliases for commands...

Once the container is comitted, you can then start a new containers from that new image.



### Removing old containers

`docker rm ...`

Where ... is either a container name, or its unique id.

### Removing an image

`docker rmi ...`

Here again, ... is either the image name, or its unique id.

