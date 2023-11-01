---
title: Setting Up git
---

# Guide: Setting Up git for CS 240

To set up git, there are certain commands you will run:

1. Create your github.com if you don't have one,
2. Once for the entire semester ("Course Setup"),
3. Once for each computer you use ("Computer Setup"),
4. Once each time you work on an assignment ("Assignment Setup")


## Creating a GitHub Account

We use a University of Illinois enterprise licensed github.com repository for your course code submissions.  You will use your own personal github.com account, but you will associate your account with your Illinois account and your CS 240 work will be isolated in the enterprise organization.

If you do not have a github.com account, you need to create a free account.  Most people use their github.com as part of their professional identity, so choosing a professional username is encouraged (ex: `wadefagen` is Wade's github, not `dy$t0pianUnIc0rN`).

- [Create your free github.com account on github.com](https://github.com/)


## Course Setup

To begin to work on assignments and turn in work, you will need to create a git repository for the course.

- Visit: [https://edu.cs.illinois.edu/create-gh-repo/sp22_cs240](https://edu.cs.illinois.edu/create-gh-repo/sp22_cs240)
- You will need to use your repository link in the steps below (highlighted in yellow)


## Computer Setup

1. On your computer, go to **your Desktop** and create a `cs240` folder on your Desktop

2. Now, going back and **using your terminal/command line**, run the following commands to navigate into your `cs240` folder:
   ```
   cd Desktop
   cd cs240
   ```

3. **Clone** a local copy of your git repository with the following command
   (making sure to replace `YOUR-GIT-REPO-URL` with the URL from the "Course Setup" above):

   <pre><code>git clone <span style="background-color: yellow">&lt;YOUR-GIT-REPO-URL&gt;</span> <span style="background-color: lime">&lt;NETID&gt;</span></code></pre>

   <pre><code># Example: git clone <span style="background-color: yellow">https://github.com/cs240-illinois/sp22_cs240_waf</span> <span style="background-color: lime">waf</span></code></pre>

   **You may get errors running this command and will need to set up your access to your github.**  Often the error message will contain a URL where you github provides you the next steps (ex: generating a token, adding OAuth permissions, etc).  This command must be successful before you can continue.


4. Once you have a clone, Navigate into your git directory by going into your NetID-named folder:

   <pre><code>cd <span style="background-color: lime">&lt;NETID&gt;</span></code></pre>


5. Set up a connection to the `release` repository where code will be released for you:
   - **If you get any output, it was NOT successful**.  *(No output means no error! :))* 
   - Read the error message you are given, git error messages are very good.
   - If you are not sure, you can run it again and it will say that "the remote already exists".

   ```
   git remote add release https://github.com/cs240-illinois/sp22_cs240_.release
   ```

6. Let git know who you are **REPLACE YOUR NAME and EMAIL with your name/email**:
   ```
   git config user.name "Your Name"
   git config user.email "netid@illinois.edu"
   ```


## Assignment Setup

Each assignment will include the git commands on the assignment page (ex: on MP1).
