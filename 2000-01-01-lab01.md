## Lab 1: Install Docker for your own Device

#### Objectives

At the end of this lab, you will have:

-   Installed Docker on your own device.

-   Run som commands to check if Docker is installed correctly.

--------

#### Step 1: Download Docker


First we must download Docker for your own device, get the right packaged for your platform:

  - [Windows 10 Professional or Enterprise 64-bit](https://download.docker.com/win/stable/InstallDocker.msi)
  - [Mac OS Yosemite 10.10.3 or above](https://download.docker.com/mac/stable/Docker.dmg)

For previous OS versions use [play-with-docker](http://labs.play-with-docker.com/).

--------

#### Step 2: Install Docker (if you don't use play-with-docker)

Double-click on `Docker.dmg` for Mac and `InstallDocker.msi` for Windows to start the install process.

When the installation finishes, Docker starts automatically. On the Mac a whale appears in the top status bar and for Windows the whale appears in the notification area. This indicates that Docker is running and accessible from a terminal.

--------

#### Step 3: Run it

Open a command-line terminal *(for Windows try PowerShell or even better install [Cygwin](https://cygwin.com/install.html))* and try out some Docker commands. Or if you use play-with-docker press +ADD NEW INSTANCE.

- Run `docker version` to check that you have the latest release installed.

```
$ docker version
Client:
 Version:      17.03.1-ce
 API version:  1.27
 Go version:   go1.7.5
 Git commit:   c6d412e
 Built:        Tue Mar 28 00:40:02 2017
 OS/Arch:      darwin/amd64

Server:
 Version:      17.03.1-ce
 API version:  1.27 (minimum version 1.12)
 Go version:   go1.7.5
 Git commit:   c6d412e
 Built:        Fri Mar 24 00:00:50 2017
 OS/Arch:      linux/amd64
 Experimental: true
```

- Run `docker run hello-world` to verify that Docker is pulling images and running as expected.

```
$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
78445dd45222: Already exists
Digest: sha256:c5515758d4c5e1e838e9cd307f6c6a0d620b5e07e6f927b07d05f6d12a1ac8d7
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://cloud.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/

```

In the next lab, we will learn how to run our first application with Docker.
