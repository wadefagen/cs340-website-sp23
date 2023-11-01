---
title: Wrapping the MP as a Docker Container

date: 2021-04-05
due: 2021-04-12 23:59:59

points: 25

number: 7.2
published: false
---

# Overview

In this MP, you will begin to explore docker and create a docker container of your MP7 solution.


## Initial Files

To generate a new repo for this assignment, go to the following [link](https://classroom.github.com/a/xcHIYj8H) and accept the assignment. After a minute or so, refresh the page. You should see your new repo has been created. Click on the green button that says `Code` and copy the clone link.

In your CS 240 directory, clone your new directory with the following command, using the link you got from the step above:

```
git clone <link>
```

Once that is done, be sure to go to the `netid.txt` file and type in your netid. This is how we will associate your GitHub account with your school id. If you forget to do this step, you will see a test failing on GitHub classroom to remind you.

You will use your MP7 code for this MP. (If you do not have a working `mp7` solution, please e-mail `waf@illinois.edu` and I'll provide a working solution for you.)


### Docker

You will need to have `docker` to complete this MP. The easiest way to install `docker` is to download the free [Docker Desktop](https://www.docker.com/products/docker-desktop).


## Machine Problem

The goal of this MP is simple: create a `Dockerfile` in your MP7.2 directory that will run your MP7 as a container. Specifically, it must:

- Launch the `courses_microservice`
- Launch your `mp7` GPA schedule calculator
- Be accessible on the host computer at `http://127.0.0.1:5000/` just like if you weren't running in docker.


### Building and Running the Docker

The docker container should be able to be built via a simple `docker build`:

```
docker build --tag cs240-mp7 .
```

Then the container should be able to be run via the following `docker run`:

```
docker run --rm -it -p 5000:5000 -p 24000:24000 cs240-mp7
```


### Creating your `Dockerfile`

You will need to create a `Dockerfile`, and possibly other files, to ensure your app builds into a docker container.

- You should not need to modify your `mp7` python code in any way to get this to work. It is okay to add additional files to your mp7.2 directory.

- This file will be slightly more complex as you will need to run two services in one container. The Docker documentation article [Run multiple services in a container](https://docs.docker.com/config/containers/multi-service_container/) can give you an idea on where to get started.

- You can start your container off using any publicly available docker image -- it may be useful to use something more than `FROM python:3.9` if you find you need additional libraries pre-installed.

- You can refer to [Lecture #18: Docker](https://courses.engr.illinois.edu/cs240/fa2020/static/lectures/Lecture%2018_%20Docker.pdf) to help out on the syntax, format, and design of Dockerfiles.

Note: You will likely have a command to start both microservices. Please add the flag ```--host=0.0.0.0``` to both commands. If you don't, there's a chance you will see a working microservice on your end in the browser, but in the tests, your code won't be able to connect to the courses_microservice.


## Submit

When you have completed your program, double-check that your server runs as expected inside a Docker container.  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com). You should also be able to see if your code passed our test cases from there. 

Note: the way our test cases work for now is by storing them in a file in your repo, called `mp7_2-courses-microservice-docker-test.py`. If you modify this file, you may see incorrect results when we run our test cases on GitHub Classroom. At the assignment deadline, we will run our test cases on your repo using a fresh copy of `mp7_2-courses-microservice-docker-test.py`.