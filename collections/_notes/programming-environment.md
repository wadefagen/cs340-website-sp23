---
title: CS 240 - Setting Up Your Programming Environment
---

# Programming Environment

Before you get started programming, you'll need to set up your programming environment. We **highly recommend** you set up your own computer as your development environment, and we believe that real-world, industry-standard tools that are used by millions of people every day are the best tools to use for your development environment. This course will focus on providing you insight into using these tools. 

To write and compile C code for this course, you will need the following tools:
- The [GNU Compiler Collection](https://gcc.gnu.org/) (GCC)
- Git Version Control
- A terminal environment
- A text editor/IDE (a popular option is [VSCode](https://code.visualstudio.com/))

We additionally use the following debugging tools:
- [Valgrind](https://valgrind.org/)
- [GDB](https://www.sourceware.org/gdb/)

Setting up an environment that can install and run those utilities will depend on what operating system you are using:

## Windows:

On Windows, we **highly** recommend setting up [Windows Subsystem for Linux 2 (WSL2)](https://docs.microsoft.com/en-us/windows/wsl/install-win10) with your favorite distribution (we recommend [Ubuntu](https://en.wikipedia.org/wiki/Ubuntu)). WSL is a virtualization tool that will allow you to run an installation of Linux, which has much better support for the tools used in this course. After setting up WSL, you may need to install the necessary software for this class using your distribution's package manager.

There two guides are useful for getting started with visual development:

- Install WSL2 and Ubuntu -- [https://docs.microsoft.com/en-us/windows/wsl/install](https://docs.microsoft.com/en-us/windows/wsl/install)
- Using C++ and WSL in VS Code ​-- [https://code.visualstudio.com/docs/cpp/config-wsl](https://code.visualstudio.com/docs/cpp/config-wsl)


<!--
- An alternative to using WSL is using the [MinGW platform](https://www.mingw-w64.org/), a Windows port of GCC. However, we **do not** reccomend you use this over WSL to develop for this course, both because MinGW is much more difficult to set up properly and because we provide no guarantee that C code which compiles and runs on native Windows will compile and run on our autograder.
-->

## Mac OSX:

If you are using a computer running MacOS, there are a few considerations to keep in mind:

- OSX is a Unix-like operating system, but is not Linux.  As a systems course, there will be times that you will encounter unexpected behavior and will have to find workarounds.  Where possible, we will support OSX as best as possible.

- However, `valgrind` does not have an official release for the most recent MacOS versions, making debugging memory-related errors impossible to do locally. If you would like to test your programs with `valgrind` (which we **highly** recommend you do!), you should compile and test your programs on EWS or another machine that can run `valgrind`.

To develop on MacOS, you'll need the following prerequisites:
- [XCode Command Line Tools](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
- The Homebrew package manager (installation instructions found [here](https://docs.brew.sh/Installation)) to `brew install g++`


## Linux:

On Linux, setting up your environment should be as simple as installing the required tools (if you do not already have them) via your distribution's package manager. For Debian-based distributions, you should take a look at the `build-essential` package to install GDB and GCC. For Arch, the `base-devel` package should install most of this course's prerequisite tools.


## Alternative: EWS Labs

If for any reason you are unable to or do not want to develop on your local machine, you may work remotely on an EWS machine. EWS will provide a limited Linux development environment which is already set up with most of the tools needed for this course. For more information, see the EngrIT guide here: [https://answers.uillinois.edu/illinois.engineering/page.php?id=81727](https://answers.uillinois.edu/illinois.engineering/page.php?id=81727).

Note: Running on EWS requires you to run `module load gcc` each time you log in to ensure you have a recent version of the compiler in use.
