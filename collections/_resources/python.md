---
title: Setting Up Python
---

# Installing Python on your VM

The OS that the our VM from EngrIT are running is `Ubuntu 16.04`.  This version of Ubuntu is from 2016 and does not have the latest versions of Python and you'll need to install a modern version of Python to run the latest Python libraries.

There's two easy ways to do this:
- Natively, through the Ubuntu `apt` package manager,
- Through a Python distribution like `miniconda`


## Installing with `apt`

To install Python using `apt`, complete the following steps on your VM:

- Run: `sudo apt update`,
- Run: `sudo apt install software-properties-common`,
- Run: `sudo add-apt-repository ppa:deadsnakes/ppa` and press `[ENTER]` to confirm the install,
- Run: `sudo apt update`,
- Run: `sudo apt install python3.9 python3.9-dev` and type `Y` to confirm the install,
- Run: `curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py`,
- Run: `python3.9 get-pip.py` to install `pip` (used to install Python libraries)

When installing using this method, you will always need to run `python3.9` to run `python` files.  (Ex: `python3.9 hello.py`.)


## Installing `miniconda`

You can find details about [miniconda on their website](https://docs.conda.io/en/latest/miniconda.html).
