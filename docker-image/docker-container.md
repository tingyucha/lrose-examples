# The Lrose-Blaze Docker image

## What is it?

Lrose-Blaze allows you to run Lrose applications without having to
compile and install lrose binaries and libraries. 

It is a Ubuntu 14.04 image, with lrose-core installed in 
/usr/local/lrose.

It has one user named **lrose** so if you don't want to run commands
as root, you can specify **-u lrose** when you `docker run` or `docker exec`

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

### Using the lrose wrapper ###

The `lrose` wrapper is a bash script that tries to make running and
managing docker containers a bit easier. Instead of having to provide
a lot of docker arguments (image name, container name, display
variable, volume mounts...) an lrose command invocation is reduced to
something like this: 

```
lrose <few_options> -- <command> <options>
```

You can run `lrose -h` to see what options the wrapper supports.

For example, to run `HawkEye` in archive mode.

```
lrose -- HawkEye -archive_url /tmp/KHGX -start_time "2017 08 00 00 00" -time_span 7200
```

The `lrose` wrapper reads in ~/.lroseargs which, for now, only supports
volume mappings, one mapping per line. For the example above, you'd
add an entry to map your data directory to /tmp/KHGX in the container.

You can also add entries for other folders you might need in the
container. For example:

```
# Location of HawkEye input radar files
/path/to/some/folder/KHGX:/tmp/KHGX
# Output directory for lrose commands
/my/path/to/large/disk:/tmp/output
# I want to be able to access my home from inside the container
/home/miko:/home/miko
```

The `lrose` wrapper will convert all of these to `--volume` options
when running a container.

### Starting an lrose-blaze container

If you decide to manage your containers yourself instead of using the `lrose` wrapper, you have a few scenarios to choose from:

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

#### Running X11 programs (such as HawkEye)

Mac OS and Linux require sligtly different invocations to be able to display on your host machine.

##### Mac OS

Find the ip address of your machine, for example if your machine is called *my_machine* use this command: `host my_machine`

Let's say your address is 192.168.0.10 you can then invoke the docker run command with the following option: `-e DISPLAY=192.168.0.10`

To run *HawkEye*, you can start the container this way: (you will of course have to add a couple of -v mappings for the input and output folders)

```
docker run --name lrose_container --user lrose \
           -e DISPLAY=192.168.0.10:0  -v /tmp/.X11-unix:/tmp/.X11-unix:rw -it \
           -w /home/lrose lrose-blaze /usr/local/lrose/bin/HawkEye
```

##### Linux

You just have to use the `--env="DISPLAY" option to the docker run command.
You might have to set QT_X11_MITSHM to 1. MIT-SHM is an extension that allows faster transaction by using shared memory. This is blocked by docker isolation, so if the flag is not set you will get permission errors.

To run *HawkEye*, you can start the container this way: (you will of course have to add a couple of -v mappings for the input and output folders)

```
docker run --name lrose_container --user lrose \
           --env="DISPLAY" --env QT_X11_NO_MITSHM=1 \
           -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
           -w /home/lrose lrose-blaze /usr/local/lrose/bin/HawkEye
```

#### Other useful options

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

   


