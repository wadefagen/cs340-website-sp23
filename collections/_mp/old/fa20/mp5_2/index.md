---
title: Wrapping the MP as a Docker Container

date: 2020-10-27
due: 2020-11-05 08:00:00

points: 25

number: 5.2
published: false
---

# Overview

In this MP, you will begin to explore docker and create a docker container of your MP5 solution.


## Initial Files

There are no additional files for this MP. You will work in your `mp5` directory for this MP. (If you do not have a working `mp5` solution, please e-mail `waf@illinois.edu` and I'll provide a working solution inside of your course git repo.)



### Docker

You will need to have `docker` to complete this MP. The easiest way to install `docker` is to download the free [Docker Desktop](https://www.docker.com/products/docker-desktop).


## Machine Problem

The goal of this MP is simple: create a `Dockerfile` in your MP5 directory that will run your MP5 as a container. Specifically, it must:

- Launch the `courses-microservice`
- Launch your `mp5` GPA schedule calculator
- Be accessible on the host computer at `http://127.0.0.1:5000/` just like if you weren't running in docker.


### Building and Running the Docker

The docker container should be able to be built via a simple `docker build`:

```
docker build --tag cs240-mp5 .
```

Then the container should be able to be run via the following `docker run`:

```
docker run --rm -it -p 5000:5000 cs240-mp5
```


### Creating your `Dockerfile`

You will need to create a `Dockerfile`, and possibly other files, to ensure your app builds into a docker container.

- You should not need to modify your `mp5` python code in any way to get this to work. It is okay to add additional files to your mp5 directory.

- This file will be slightly more complex as you will need to run two services in one container. The Docker documentation article [Run multiple services in a container](https://docs.docker.com/config/containers/multi-service_container/) can give you an idea on where to get started.

- You can start your container off using any publicly available docker image -- it may be useful to use something more than `FROM python:3.9` if you find you need additional libraries pre-installed.

- You can refer to [Lecture #18: Docker](https://courses.engr.illinois.edu/cs240/fa2020/static/lectures/Lecture%2018_%20Docker.pdf) to help out on the syntax, format, and design of Dockerfiles.


## Submit

When you have completed your program, double-check that your server runs as expected in a docker container. When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo via the web interface here: [https://github-dev.cs.illinois.edu/cs240-fa20](https://github-dev.cs.illinois.edu/cs240-fa20)