# Pawsey Nimbus HPC Docker Containers Introduction
Craig Duncan
5 March 2019 (draft)
This note requires review for currency.

# Overview

Intro[https://docs.docker.com/get-started/] 
CheatSheet[https://github.com/wsargent/docker-cheat-sheet]

Note: Docker will work for simple setups, but in time, Docker Compose will need to be used as well, to handle multiple containers and options.
1. Register with Docker Hub
2. Install Docker on Nimbus Instance
3. Install Docker Compose on Nimbus Instance.
4. Install Docker and Compose on local workstation (both needed?)
5. Test installations
6. Obtain a standard Docker Image and load that on to instance (options: pull down from Docker Hub ;
include Pawsey images)
7. Locate specific Bioinformatics Docker Images to use as templates
8. Build your own Docker Image (requires creating DockerFile before build)

# Docker Hub
A service provided by Docker for finding and sharing containters.

Uses push and pull commands (like git).

DockerHub[https://docs.docker.com/docker-hub/]

You will need a Docker Hub account to use it.

# Pawsey Nimbus Environment (Virtual Machines)

For this, I assume that the Nimbus instance is set up as a virtual machine and we are just interested in creating a new Docker Container to use. The instance should have a Docker Engine installed. We can check this on the Nimbus instance with:
	docker --version

If not installed, we need to install Docker on the Nimbus instance. This should be possible using:
sudo apt-get install docker at the command line.

See this general link for updating the APT package lists before installs:

PawseyInstanceSetup[https://pawseysupercomputing.github.io/using-nimbus/08-maintaining-your-instance/
index.html]

NB: Pawsey is using ‘Shifter’ for managing containers, including Docker containers, on Galaxy, Magnus and Zeus Check[https://support.pawsey.org.au/documentation/display/US/Containers]

Documented example of Magnus and Shifter[http://www.underworldcode.org/posts/Shifter_and_how_to_
run_underworld_on_HPC]

# Docker Compose

An important step for future team resource management will be using Docker Compose to manage container resources.

Pawsey recommended setup is to install both Docker and Docker Compose (https://www.docker.com) on
own workstation.

Compose allows documentation and configuration of multiple container options.

	DockerCompose[https://docs.docker.com/compose/overview/]

Also, note that there is (since 2014) an open-source container orchestration system for automating deployoment of containers called ‘Kubernetes’ and it runs with Docker containers. This guide only deals with using Docker directly.

# Workstation Docker installation

	DockerInstall[https://docs.docker.com/install/overview/]

You will need Docker (https://www.docker.com/) on Linux or Docker Toolbox (https://www.docker.com/
products/docker-toolbox) on Windows/Mac. Use Docker Community Edition to get started:
DockerCE[https://hub.docker.com/search/?type=edition&offering=community]
Mac[https://docs.docker.com/docker-for-mac/install/] 
MacDownload[https://hub.docker.com/editions/
community/docker-ce-desktop-mac] 
(Option)Mac_DockerDesktop[https://docs.docker.com/docker-for-mac/
docker-toolbox/]

NB: A Local Host for Docker Engine/Machine
For testing a Docker deployment on a local machine (before sending to an off-site server), Docker Machine was an option (now Docker Desktop enables the same?) DockerMachine[https://docs.docker.com/machine/overview/]

# Test Docker installation on Nimbus VM/local machine

## Instructions
1. setup docker on local machine
2. docker command to pull a public docker (image): docker pull bioconductor/devel_base2
OR
2. Run a test image (hello world)

## Install checks

type this to check version after install: docker --version
Try to run an image: if Docker cannot find it, it will pull it down for you
docker run hello-world

For more examples and ideas, visit: https://docs.docker.com/get-started/

# Simple docker image for web server

	docker run --detach --publish=80:80 --name=webserver nginx 

Then type: 
	
	http://localhost/ 

in the browser to view the nignx homepage. The default port is already specified as 80.

Example ggsashimi

https://hub.docker.com/r/guigolab/ggsashimi/

Docker Hub includes one private repository for free[https://docs.docker.com/docker-hub/upgrade/]
1. Pull image: docker pull guigolab/ggsashimi
2. Build iamge:
cd ggsashimi docker build -f docker/Dockerfile -t guigolab/ggsashimi

# Bioinformatics containers/images

1. Bioconductor container (R)
2. Pawsey basic python container (Python)
3. Biopython container (Python)

## Bioconductor test/base container

BioconductorDocker[https://bioconductor.org/help/docker/]
e.g. use the bioconductor/devel_base2 image.

Base2[https://hub.docker.com/r/bioconductor/devel_base2/]

## Pawsey python container
PawseyPythonDocker[https://hub.docker.com/r/pawsey/hpc-python/]

nb you can see all Pawsey Docker Containers by doing a search on Docker Hub:

PawseyContainers[https://hub.docker.com/search?q=pawsey&type=image]

# Biopython test/base container

There are some instructions for pulling and running different Biopython container options at this address:

BioPythonContainer[https://github.com/biopython/biopython_docker]
Y
ou can also go directly to these containers on the Docker Hub by searching for ‘biopython’ in the Docker Hub search box or use this URL directly:

	SearchBiopythonDockerHub[https://hub.docker.com/search?q=biopython&type=image]

# Building your own docker image

## Build commands and DockerFile

see also docker build --help

The docker build -f /path/to/a/Dockerfile command uses a DockerFile to take information about the
path or URL (git site) used to build the image.

To specify the repository (i.e. the ‘tag’ at which to save the new image if the build succeeds) type:

	docker build -t test/myapp

DockerBuilder[https://docs.docker.com/engine/reference/builder/] 
BestPractices[https://docs.docker.com/develop/develop-images/dockerfile_best-practices/]

	“When building an image, Docker steps through the instructions in your Dockerfile, executing each in the order specified. As each instruction is examined, Docker looks for an existing image in its cache that it can reuse, rather than creating a new (duplicate) image.” (cheche can be turned off)

DockerFiles may include commands like ADD, COPY, ENTRYPOINT, FROM, ARG, RUN, CMD, LABEL,
EXPOSE, ENV. 

	"All these commands are to be listed (i.e. written) successively, inside a single plain text file(i.e. Dockerfile), in the order you would like them performed (i.e. executed) by the docker daemon to build an image."
(https://www.digitalocean.com/community/tutorials/docker-explained-using-dockerfiles-to-automate-building-of-images)
nb 2013 - may be deprecated.

MAINTAINER is the author of the image. RUN is followed by a command line instruction, as you would
enter at command line. ENTRYPOINT is where the container will start. It is ok to put this as last command.

The DockerFile must start with “FROM”. # is a comment character (if at start of line). e.g.

	# FROM microsoft/nanoserver COPY testfile.txt c:\ RUN dir c:\

The base image in the ‘FROM’ could be a container that is already created. Other commands can then be
executed to add to that base image. i.e. copy files to the image.

## Notes on running docker images between logins

[TO DO].
