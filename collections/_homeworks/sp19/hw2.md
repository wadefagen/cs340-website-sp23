---
title: Simpson's Paradox

date: 2019-02-05
due: 2019-02-10

order: 2
published: false
---

# Homework 2: Simpson's Paradox

The second homework in Data Science Discovery helps you understand Simpson's Paradox.


## Step 1: Retrieve the lab using git

Using your command line, navigate to your `stat107` repository (`cd Desktop` -> `cd stat107` -> `cd [NETID]`) and fetch the notebook from our release repository by running the following two `git` commands:

```
git fetch release
git merge release/hw2 -m "Merging initial files"
```

**ONLY IF you get an error** related to unrelated histories, use:

```
git merge release/hw2 --allow-unrelated-histories -m "Merging initial files" 
```


## Step 2: Open the notebook

Open the notebook with the command:

```
jupyter notebook
```

Inside of the notebook webpage:

- Navigate into `hw2`
- Open up the `hw2.ipynb` notebook
- Follow the instructions inside of the notebook

Whenever you are done, you should **checkpoint** (using File -> Save Checkpoint in the notebook) your notebook to save your work.  Once your work is saved, you can:

- Use `File` -> `Close and Halt` on the notebook
- Use `Quit` (in the top-right) on the directory view to completely exit jupyter



## Step 3: Submitting your work

When you're ready to save your work online and/or submit your work, return to the command line and run:

```
git add -A
git commit -m "submission (or any message here)"
git push origin master
```
