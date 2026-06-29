Dockerfile 101

# How to make a Dockerfile

## Commands:
ADD [Op] <src> <dest> (where dest is present in the file system of docker container)
* Copies files or dir from src to dest. Can be copied from build context, remote url or git repo

COPY [OP] <src> <dest> (where dest is present in the file system of docker container)
* same as ADD but can only do basic copy from within the build context.

CMD ["main-command","parameter1","para2"]
* single use command in a Dockerfile sets the main command to be run when running a container from an image.
* different from RUN as this is the main command for the image unlike RUN.
* CMD is overridable. if you specify a command at runtime it is overridden.

RUN [OP] <command>
* run any kind of command in the file. Not as CMD or ENTRYPOINT and can be used again and again as per req.

ENTRYPOINT
* configures a container to run as an executable. specifies main command that will always run when it starts.
* ENTRYPOINT is not overridable. commands passed at runtime as sent through the ENTRYPOINT rather than around it. E.g. docker run -it --entrypoint /bin/bash my-image

** P.S. best practice is to combine CMD and ENTRYPOINT.

ENV <key>=<value>
* .env file contents in a dockerfile

EXPOSE <port>/<protocol>
* infroms docker which ports to listen to during runtime.

FROM <image> AS <name>
* specifies the build stage and sets base image
* valid Dockerfile must start with a FROM command. only ARGS can preceed the FROM.
* E.g. FROM python:3.12-slim
* E.g. 	ARG VERSION=3.13
	FROM python:${VERSION}
	
SHELL ["executable", "para"]
* overrides default shell used for shell commands

VOLUME ["/data"]
* Creates a volume at runtime.

WORKDIR /path
* specifies the work directory in which dockerfile will be executed.
* if it does not exist, docker will automatically create the command.
* it is a good practice to set your workdir explicitly.

## Dockerfile Format

* Always write your Instructions in uppercase and then the specifications. E.g. FROM python:3.12-slim
* Must start a file using FROM or preceded with ARG command.
* # is used for commenting.

Parser directive:
	* Optional lines used to define how Dockerfile is handled.
	* Should always be at the top of the file.
	1. syntax=docker/dockerfile:1 (used to declare dockerfile syntax version)
	2. escape=\ (escape character to escape characters or newline)
	3. check
	
* The --no-cache flag disables the build cache, forcing Docker to rebuild all layers from scratch
E.g. docker build --no-cache -t my-image:my-tag .

## Examples:

Project req: node, nginx, jsx files and css files
Dockerignore: node_modules, README.md, venv, .git

We need to first install npm using package.json and package_lock.json, then copy the project files and do npm run build. Finally we can setup the nginx using the first docker config and expose port on which we want to run.

<<<
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build (this converts jsx code into normal html css files)

FROM nginx

COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 80
>>>
