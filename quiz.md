## Quiz

Following Docker command:docker exec -it container_id bash is used to:

A. Activate default VM machine
####B. Access a running container
C. Build an image
D. Commit changes done in an docker image

 
 Following Docker command:docker push user_name/repository_name is used to:
A. 
Activate default VM machine
####B. 
Push changes done in an docker image into Docker Hub
C. 
Build an image
D. 
Commit changes done in an docker image


Which of the following is True? In Docker, when a container is exited


a.the state of the filesystem and the processes are preserved

b.the state of the filesystem is not stored but the state of the processes are stored.

####c.the state of the filesystem is stored but the processes are not.

ik zeg ###d. the state of the filesystem and the processes are not preserved


Which of the following command is used to associate an image with a repository (or multiple repositories) at build time?


## sudo docker build -t IMAGENAME

sudo docker tag IMAGEID IMAGENAME

sudo docker commit CONTAINERID IMAGENAME


What is the command used to convert a container into an image?


docker diff

###docker commit

docker push

 docker run
 
 
 The default registry is accessible using the command:


docker run search

docker ls

## docker search

docker ps (docker images)


Which of the following is true?


docker ps shows all containers by default

### docker ps shows all running containers by default.


When creating a Dockerfile, which instruction is used to declare the base image?

### IMPORT <base image>


FROM <base image>

DOCKER IMPORT <base image>

None of the above

What is the difference between a Docker image and a Docker container?

Those terms are synonymous


A Docker container is a runtime instance of an image

#### A Docker image is a runtime instance of a container

Docker images are managed within a Docker container

Which of the following can be used to purge all stopped containers?


### docker rm $(docker ps -a -q)

docker rm -f stop-all

docker delete all

docker —delete


Which of the following is the command used to restart a Docker image?


### docker start <imageId>

docker restart <imageId>

An image is not restarted it is merely a union of layered filesystems.

docker-restart <imageId>

What happens when the following command is executed: "docker build --rm=true -t rhtgptetraining/lovelxc ."

This is an invalid command


#### Docker attempts to build an image from a Dockerfile that presumably exists in the current directory

Docker attempts to spin up a container with a tag name of: rhtgptetraining/lovelxc

Docker attempts to build a container called: rhtgptetraining/lovelxc

Which of the following could be used to smoke test the Docker service?

docker test hello-world

docker smoketest


### docker run hello-world


none of the above
