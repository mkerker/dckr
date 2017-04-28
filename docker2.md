## Lab 7: CMD and ENTRYPOINT

#### Objectives

In this lab, we will learn about two important Dockerfile commands: `CMD` and `ENTRYPOINT`.

Those commands allow us to set the default command to run in a container.


#### Step 1: Defining a default command

When people run our container, we want to greet them with a nice hello message, and using a custom font.

For that, we will execute:
`figlet -f script hello`

-   `-f` script tells figlet to use a fancy font.

-   hello is the message that we want it to display.

 Adding CMD to our Dockerfile

Our new Dockerfile will look like this:

```
 FROM ubuntu

 RUN apt-get update

 RUN \["apt-get", "install", "figlet"\]

 CMD figlet -f script hello
```

-   CMD defines a default command to run when none is given.

-   It can appear at any point in the file.

-   Each CMD will replace and override the previous one.

-   As a result, while you can have multiple CMD lines, it is useless.

#### Step 2: Build and test our image

Let's build it:

```
 $ docker build -t figlet .
 ...
 Successfully built 042dff3b4a8d
```

And run it: 

```
$ docker run figlet 
```

If we want to get a shell into our container (instead of running figlet), we just have to specify a different program to run:
```
 $ docker run -it figlet bash 
 root@7ac86a641116:/
```
-   We specified bash.

-   It replaced the value of CMD.

#### Step 3: Using ENTRYPOINT

We want to be able to specify a different message on the command line, while retaining figlet and some default parameters.

In other words, we would like to be able to do this:
```
 $ docker run figlet salut
```

We will use the `ENTRYPOINT` verb in Dockerfile.

Adding` ENTRYPOINT` to our Dockerfile

Our new Dockerfile will look like this:
```
 FROM ubuntu

 RUN apt-get update

 RUN \["apt-get", "install", "figlet"\]

 ENTRYPOINT \["figlet", "-f", "script"\]
```
-   `ENTRYPOINT` defines a base command (and its parameters) for the container.

-   The command line arguments are appended to those parameters.

-   Like `CMD`, `ENTRYPOINT` can appear anywhere, and replaces the previous value.

Why did we use JSON syntax for our `ENTRYPOINT`?


 Implications of JSON vs string syntax

-   When `CMD` or `ENTRYPOINT` use string syntax, they get wrapped in sh -c.

-   To avoid this wrapping, you must use JSON syntax.

What if we used ENTRYPOINT with string syntax?

```
 $ docker run figlet salut
```

This would run the following command in the figlet image:
```
 sh -c "figlet -f script" salut
```
Build and test our image

Let's build it:
```
 $ docker build -t figlet .

 ...

 Successfully built 36f588918d73
```
And run it:
```
 $ docker run figlet salut
```

Great success!

#### Step 4: Using CMD and ENTRYPOINT together

What if we want to define a default message for our container? Then we will use `ENTRYPOINT` and `CMD` together.

-   `ENTRYPOINT` will define the base command for our container.

-   `CMD` will define the default parameter(s) for this command.

-   They *both* have to use JSON syntax.

Our new Dockerfile will look like this:

```
 FROM ubuntu

 RUN apt-get update

 RUN \["apt-get", "install", "figlet"\]

 ENTRYPOINT \["figlet", "-f", "script"\]

 CMD \["hello world"\]
```

-   `ENTRYPOINT` defines a base command (and its parameters) for the container.

-   If we don't specify extra command-line arguments when starting the container, the value of `CMD` is appended.

-   Otherwise, our extra command-line arguments are used instead of `CMD`.

#### Step 5: Build and test our image

Let's build it:
```
 $ docker build -t figlet .

 ...

 Successfully built 6e0b6a048a07
```
And run it:

```
$ docker run figlet
```

```
 $ docker run figlet hola mundo
```

> Overriding ENTRYPOINT

What if we want to run a shell in our container?

We cannot just do docker run figlet bash because that would just tell figlet to display the word "bash."

We use the --entrypoint parameter:
```
 $ docker run -it --entrypoint bash figlet 
 root@6027e44e2955
```

## Lab 8: Copying files during the build

#### Objectives

So far, we have installed things in our container images by downloading packages. We can also copy files from the *build context* to the container that we are building.

Remember: the *build context* is the directory containing the Dockerfile. In this chapter, we will learn a new Dockerfile keyword: COPY.


 Build some C code

We want to build a container that compiles a basic "Hello world" program in C. Here is the program, hello.c:

```
 int main () 
 { puts("Hello, world!"); return 0;
 }
```

Let's create a new directory, and put this file in there.

Then we will write the Dockerfile.

 The Dockerfile

On Debian and Ubuntu, the package build-essential will get us a compiler.

When installing it, don't forget to specify the -y flag, otherwise the build will fail (since the build cannot be interactive).

Then we will use `COPY` to place the source file into the container.
```
 FROM ubuntu

 RUN apt-get update

 RUN apt-get install -y build-essential

 COPY hello.c /

 RUN make hello

 CMD /hello
```

Create this Dockerfile.

 Testing our C program

-   Create hello.c and Dockerfile in the same direcotry.

| •   | Run docker   | build -t hello . in this directory.        |
|-----|--------------|--------------------------------------------|
| •   | Run docker   | run hello, you should see Hello, world!.   |

Success!

 `COPY` and the build cache

-   Run the build again.

-   Now, modify hello.c and run the build again.

-   Docker can cache steps involving COPY.

-   Those steps will not be executed again if the files haven't been changed.

 Details

-   You can `COPY` whole directories recursively.

-   Older Dockerfiles also have the `ADD` instruction. It is similar but can automatically extract archives.

-   If we really wanted to compile C code in a compiler, we would:

    -   Place it in a different directory, with the WORKDIR instruction.

    -   Even better, use the gcc official image.

## Lab 9: Advanced Dockerfiles

#### Objectives

We have seen simple Dockerfiles to illustrate how Docker build container images. In this chapter, we will see:

-   The syntax and keywords that can be used in Dockerfiles.

-   Tips and tricks to write better Dockerfiles.

 Dockerfile usage summary

-   Dockerfile instructions are executed in order.

-   Each instruction creates a new layer in the image.

-   Instructions are cached. If no changes are detected then the instruction is skipped and the cached layer used.

-   The `FROM` instruction MUST be the first non-comment instruction.

-   Lines starting with \# are treated as comments.

-   You can only have one CMD and one ENTRYPOINT instruction in a Dockerfile.

 The FROM instruction

-   Specifies the source image to build this image.

-   Must be the first instruction in the Dockerfile, except for comments.

The FROM instruction

Can specify a base image:

 `FROM ubuntu`

An image tagged with a specific version:

 `FROM ubuntu:12.04`

A user image:

 `FROM training/sinatra`

Or self-hosted image:

` FROM localhost:5000/funtoo`

 More about FROM

-   The `FROM` instruction can be specified more than once to build multiple images.

 `FROM ubuntu:14.04`

 . . .

 `FROM fedora:20`

 . . .

 Each `FROM` instruction marks the beginning of the build of a new image.

 The `-t` command-line parameter will only apply to the last image.

-   If the build fails, existing tags are left unchanged.

-   An optional version tag can be added after the name of the image. E.g.: ubuntu:14.04.

 A use case for multiple FROM lines

-   Integrate CI and unit tests in the build system

 FROM <baseimage>

 RUN <install dependencies>

 COPY <code>

 RUN <build code>

 RUN <install test dependencies>

 COPY <test data sets and fixtures>

 RUN <unit tests>

 FROM <baseimage>

 RUN <install dependencies>

 COPY <vcode>

 RUN <build code>

 CMD, EXPOSE ...

-   The build fails as soon as an instructions fails

-   If RUN <unit tests> fails, the build doesn't produce an image


-   If it succeeds, it produces a clean image (without test libraries and data)

 The MAINTAINER instruction

The MAINTAINER instruction tells you who wrote the Dockerfile.

 MAINTAINER Docker Education Team <education@docker.com>

It's optional but recommended.

 The RUN instruction

The RUN instruction can be specified in two ways.

With shell wrapping, which runs the specified command inside a shell, with /bin/sh -c:

 RUN apt-get update

Or using the exec method, which avoids shell string expansion, and allows execution in images that don't have /bin/sh:

 RUN \[ "apt-get", "update" \]

 More about the RUN instruction

RUN will do the following:

-   Execute a command.

-   Record changes made to the filesystem.

-   Work great to install libraries, packages, and various files.

RUN will NOT do the following:

-   Record state of *processes*.

-   Automatically start daemons.

If you want to start something automatically when the container runs, you should use

CMD and/or ENTRYPOINT.

 Collapsing layers

It is possible to execute multiple commands in a single step:

 RUN apt-get update && apt-get install -y wget && apt-get clean

It is also possible to break a command onto multiple lines: It is possible to execute multiple commands in a single step:

 RUN apt-get update \\

-   apt-get install -y wget \\

-   apt-get clean

 The EXPOSE instruction

The EXPOSE instruction tells Docker what ports are to be published in this image.

 EXPOSE 8080

 EXPOSE 80 443

 EXPOSE 53/tcp 53/udp

-   All ports are private by default.

-   The Dockerfile doesn't control if a port is publicly available.

-   When you docker run -p <port> ..., that port becomes public.

 (Even if it was not declared with EXPOSE.)

-   When you docker run -P ... (without port number), all ports declared with EXPOSE become public.

A *public port* is reachable from other containers and from outside the host.

A *private port* is not reachable from outside.

 The COPY instruction

The COPY instruction adds files and content from your host into the image.

 COPY . /src

This will add the contents of the *build context* (the directory passed as an argument to docker build) to the directory /src in the container.

Note: you can only reference files and directories *inside* the build context. Absolute paths are taken as being anchored to the build context, so the two following lines are equivalent:

 COPY . /src

 COPY / /src

Attempts to use .. to get out of the build context will be detected and blocked with Docker, and the build will fail.

Otherwise, a Dockerfile could succeed on host A, but fail on host B.

 ADD

ADD works almost like COPY, but has a few extra features.

ADD can get remote files:

 ADD http://www.example.com/webapp.jar /opt/

This would download the webapp.jar file and place it in the /opt directory.

ADD will automatically unpack zip files and tar archives:

 ADD ./assets.zip /var/www/htdocs/assets/

This would unpack assets.zip into /var/www/htdocs/assets.

*However,* ADD will not automatically unpack remote archives.

 ADD, COPY, and the build cache

-   For most Dockerfiles instructions, Docker only checks if the line in the Dockerfile has changed.

-   For ADD and COPY, Docker also checks if the files to be added to the container have been changed.

-   ADD always need to download the remote file before it can check if it has been changed. (It cannot use, e.g., ETags or If-Modified-Since headers.)

 VOLUME

The VOLUME instruction tells Docker that a specific directory should be a *volume*.

 VOLUME /var/lib/mysql

Filesystem access in volumes bypasses the copy-on-write layer, offering native performance to I/O done in those directories.

Volumes can be attached to multiple containers, allowing to "port" data over from a container to another, e.g. to upgrade a database to a newer version.

It is possible to start a container in "read-only" mode. The container filesystem will be made read-only, but volumes can still have read/write access if necessary

 The WORKDIR instruction

The WORKDIR instruction sets the working directory for subsequent instructions.

It also affects CMD and ENTRYPOINT, since it sets the working directory used when starting the container.

 WORKDIR /src

You can specify WORKDIR again to change the working directory for further operations.

 The ENV instruction

The ENV instruction specifies environment variables that should be set in any container launched from the image.

 ENV WEBAPP\_PORT 8080

This will result in an environment variable being created in any containers created from this image of

 WEBAPP\_PORT=8080

You can also specify environment variables when you use docker run.

 $ docker run -e WEBAPP\_PORT=8000 -e WEBAPP\_HOST=www.example.com ...

The USER instruction

The USER instruction sets the user name or UID to use when running the image.

It can be used multiple times to change back to root or to another user.

The CMD instruction

The CMD instruction is a default command run when a container is launched from the image.

 CMD \[ "nginx", "-g", "daemon off;" \]

Means we don't need to specify nginx -g "daemon off;" when running the container.

Instead of:

 $ docker run <dockerhubUsername>/web\_image nginx -g "daemon off;"

We can just do:

 $ docker run <dockerhubUsername>/web\_image

More about the CMD instruction

Just like RUN, the CMD instruction comes in two forms. The first executes in a shell:

 CMD nginx -g "daemon off;"

The second executes directly, without shell processing:

 CMD \[ "nginx", "-g", "daemon off;" \]

Overriding the CMD instruction

The CMD can be overridden when you run a container.

 $ docker run -it <dockerhubUsername>/web\_image bash

Will run bash instead of nginx -g "daemon off;".

The ENTRYPOINT instruction

The ENTRYPOINT instruction is like the CMD instruction, but arguments given on the command line are *appended* to the entry point.

Note: you have to use the "exec" syntax (\[ "..." \]).

 ENTRYPOINT \[ "/bin/ls" \]

If we were to run:

 $ docker run training/ls -l

Instead of trying to run -l, the container will run /bin/ls -l.

Overriding the ENTRYPOINT instruction

The entry point can be overriden as well.

| $    | docker   | run    | -it     | training/ls         | proc          | run    | srv   | tmp | var   |     |
|------|----------|--------|---------|---------------------|---------------|--------|-------|-----|-------|-----|
| bin  | dev      | home   | lib64   | mnt                 |               |        |       |     |       |     |
| boot | etc      | lib    | media   | opt                 | root          | sbin   | sys   | usr |       |     |
| $    | docker   | run    | -it     | --entrypoint bash   | training/ls   |        |       |     |

 root@d902fb7b1fc7:/\#

 How CMD and ENTRYPOINT interact

The CMD and ENTRYPOINT instructions work best when used together.

 ENTRYPOINT \[ "nginx" \]

 CMD \[ "-g", "daemon off;" \]

The ENTRYPOINT specifies the command to be run and the CMD specifies its options.

On the command line we can then potentially override the options when needed.

 $ docker run -d <dockerhubUsername>/web\_image -t

This will override the options CMD provided with new flags.

 Advanced Dockerfile instructions

-   ONBUILD lets you stash instructions that will be executed when this image is used as a base for another one.

-   LABEL adds arbitrary metadata to the image.

-   ARG defines build-time variables (optional or mandatory).

 • STOPSIGNAL sets the signal for docker stop (TERM by default).

-   HEALTHCHECK defines a command assessing the status of the container.

-   SHELL sets the default program to use for string-syntax RUN, CMD, etc.

 The ONBUILD instruction

The ONBUILD instruction is a trigger. It sets instructions that will be executed when another image is built from the image being build.

This is useful for building images which will be used as a base to build other images.

 ONBUILD COPY . /src

-   You can't chain ONBUILD instructions with ONBUILD.

-   ONBUILD can't be used to trigger FROM and MAINTAINER instructions.

 Building an efficient Dockerfile

-   Each line in a Dockerfile creates a new layer.

-   Build your Dockerfile to take advantage of Docker's caching system.

-   Combine multiple similar commands into one by using && to continue commands and \\ to wrap lines.

-   COPY dependency lists (package.json, requirements.txt, etc.) by themselves to avoid reinstalling unchanged dependencies every time.

 Example "bad" Dockerfile

The dependencies are reinstalled every time, because the build system does not know if requirements.txt has been updated.

```
 FROM python

 MAINTAINER Docker Workshop <info@hcs-company.com>

 COPY . /src/

 WORKDIR /src

 RUN pip install -qr requirements.txt

 EXPOSE 5000

 CMD \["python", "app.py"\]
```
 Fixed Dockerfile

Adding the dependencies as a separate step means that Docker can cache more efficiently and only install them when requirements.txt changes.
```
 FROM python

 MAINTAINER Docker Workshop <info@hcs-company.com>

 COPY ./requirements.txt /tmp/requirements.txt

 RUN pip install -qr /tmp/requirements.txt

 COPY . /src/

 WORKDIR /src

 EXPOSE 5000

 CMD \["python", "app.py"\]
```
