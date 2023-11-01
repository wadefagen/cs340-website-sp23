---
title: MIX Project Part IV - Course-Wide MIX

date: 2021-12-04
due: 2021-12-10 19:00:00

points: 50

layout: project

number: Week 4
published: false
---

<style>
.mix {
  font-weight: bold;
  font-size: 120%;
  line-height: 80%;
  height: 80%;
  background: -webkit-linear-gradient(#13294B, #FF552E, #13294B, #FF552E);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  font-family: Montserrat;
}
</style>

# Overview

This is the final week of <span class="mix">MIX</span>!  You will:

1. Deploy your IMs on your CS 240 virtual machine,
2. Re-factor your IMs to connect with the course-wide microservice,
3. Ensure your IMs are connected to the course-wide microservice for the final project at 7:00pm on Friday, Dec. 10 in 2035 CIF,
4. Be prepared to talk about your IMs during the final project class at 7:00pm on Friday, Dec. 10


## Requirement 1: Deploy your IM on your CS 240 VM

You have a Virtual Machine (VM) provided for you as part of being in CS 240.  This VM is similar to an AWS EC2 instance, except that it's on our "private cloud" instead of the AWS "public cloud".

- EngrIt has created a ["Power On / Reset VM"](https://answers.uillinois.edu/illinois.engineering/page.php?id=108475) guide that details how to turn on your CS 240 VM.

- The VM interface and your VM can only be accessed **either on campus or via VPN**.  If you are not on campus, [information about the UIUC VPN client is available here](https://answers.uillinois.edu/98773).

- Once you have powered on the your box, you can SSH into it with your NetID and password.

- You will likely need to install Python 3.9 on this machine since the default version of `python3` is only 3.6.  [This guide worked for me to install Python 3.9.](https://tecadmin.net/install-python-3-9-on-centos/).  Once Python 3.9 is installed, `python3.9 -m pip install flask` can be used to install flask and any other requirements.


## Requirement 2: Connect with the Course-Wide Microservice

A course-wide <span class="mix">MIX</span> server is hosted at `http://cs240-adm-01.cs.illinois.edu:5000`.  You must configure your IMs to connect to this server -- the server is running the version of hte server detailed in Week 3.

- This machine can only be accessed **either on campus or via VPN**.  If you are not on campus, [information about the UIUC VPN client is available here](https://answers.uillinois.edu/98773).

- I **highly** recommend that you use a `.env` or other config to easily switch between testing your microservice locally with localhost and connecting it to the course-wide microservice.

- Since this is a shared server, something might happen to cause it to crash and be unavailable for some time.  Use #project-mix on Discord to check with others on the server status.


## Requirement 3 and 4: Dec. 10 at 7:00pm

The final project presentation is during our final exam period at **Dec. 10 at 7:00pm**.

- Ensure your IMs are connected to the course-wide MIX server before 7:00pm, running on your VM.

- Make sure you come to lecture in 2035 CIF.

- Be ready to talk about your IMs.



## Submission

Make sure your final IM code is committed.  **If you are working on a team, make sure this is in your team repo.**  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MIX submission"
git push
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com).

