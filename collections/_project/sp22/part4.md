---
title: Project Part IV - Finale!

date: 2022-04-29
due: 2022-05-06 19:00:00

points: 70

layout: project

number: Week 4
published: false
---

<style>
.maze {
  font-weight: bold;
  /*
  font-size: 120%;
  line-height: 80%;
  height: 80%;
  background: -webkit-linear-gradient(#13294B, #FF552E, #13294B, #FF552E);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  font-family: Montserrat;
  */
}
</style>

# Overview

In this final week, you will:

1. Deploy your MGs on your CS 240 virtual machine,
2. Re-factor your MGs to connect with the course-wide microservice,
3. Ensure your MGs are connected to the course-wide microservice for the final project at 7:00pm on Friday, May 6
4. Be prepared to talk about your MGs during the final project class at 7:00pm on Friday, May 6


<div class="alert alert-info" role="alert">
  <h3>Dry Run as Part of Tuesday's Lecture!</h3>
  <p>
    As part of Tuesday's lecture, we will do a "dry run" of the shared middleware running on http://cs240-adm-01.cs.illinois.edu:5000 live in lecture.  If you have finished Part #1 and Part #2, you can ensure your MGs connect and you're ready to go for Friday. :)
  </p>
</div>


## Requirement 1: Deploy your MG on your CS 240 VM

You have a Virtual Machine (VM) provided for you as part of being in CS 240.  This VM is similar to an AWS EC2 instance, except that it's on our "private cloud" instead of the AWS "public cloud".

- EngrIt has created a ["Power On / Reset VM"](https://answers.uillinois.edu/illinois.engineering/page.php?id=108475) guide that details how to turn on your CS 240 VM.

- The VM interface and your VM can only be accessed **either on campus or via VPN**.  If you are not on campus, [information about the UIUC VPN client is available here](https://answers.uillinois.edu/98773).

- Once you have powered on the your box, you can SSH into it with your NetID and password.

- You will likely need to install Python 3.9 on this machine since the default version of `python3` is only 3.6.  [This guide worked for me to install Python 3.9.](https://tecadmin.net/how-to-install-python-3-9-on-ubuntu-18-04/).  Once Python 3.9 is installed, `python3.9 -m pip install flask` can be used to install flask and any other requirements.


## Requirement 2: Connect with the Course-Wide Microservice

A course-wide infinite maze server will be hosted at `http://sp22-cs240-adm.cs.illinois.edu:24000/`.  You must configure your MGs to connect to this server -- the server is running the course-wide shared middleware you worked with in Week #3.

- This machine can only be accessed **either on campus or via VPN**.  If you are not on campus, [information about the UIUC VPN client is available here](https://answers.uillinois.edu/98773).

- I **highly** recommend that you use a `.env` or other config to easily switch between testing your microservice locally with localhost and connecting it to the course-wide microservice.

- Since this is a shared server, something might happen to cause it to crash and be unavailable for some time.  Use Discord to check with others on the server status. :)



## Requirement 3 and 4: May 6 at 7:00pm

The final project presentation is during our final exam period at **May. 6 at 7:00pm**.

- Ensure your MGs are connected to the course-wide service before 7:00pm, running on your VM.

- **EVERYONE** must run their own MGs on their own VMs -- you cannot have one person in the group run the MGs for the entire group.

- Be prepared to speak briefly about your MG.  We will go through everyone's MG and see your dynamic vs. static MGs you have developed and see the maze generated live before we deploy the full maze.


## Submission

When you have completed your program, double-check that your server runs as expected by the specifications above.  **If you are working on a team, make sure this is in your team repo.**  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "submission"
git push
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com).

