---
title: Visual Debugger in VS Code
---

# Visual Debugger in VS Code

In CS 240, we highly recommend setting up a visual debugger for the MPs. While the command line version of the debugger is equally useful, its interface can sometimes be quite difficult to navigate. There are a lot of tools out there to help make the debugging process a little easier for you. For example, VS Code provides a visual debugger interface with some great features. 

In this guide, we will help you quickly set up the visual debugger in VS Code and introduce you to its interface. For more detailed inforamtion on the various debugging features, please check out the [official documentation from VS Code](https://code.visualstudio.com/docs/editor/debugging). 


## Debugging C

In this section, we will go over how to set up and use the debugger for the C MPs. 


### Setup

VS Code has built-in debugging support for languages like Javascript. To extend VS Code to support debugging C programs: 

- Launch VS Code. Note that if you are on Windows, you need to [launch VS Code from WSL](https://code.visualstudio.com/docs/cpp/config-wsl#_run-vs-code-in-wsl)
- Install the [C/C++ extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)


### How to Use

In the MP starter files, we have included custom configuration files (`launch.json` and `tasks.json` inside the `.vscode` folder) which will allow you to run your program and test with the debugger. Note that for VS Code to find and use our configuration settings, you need to **open the MP folder as the root folder**. 

To launch the debugger:

1. Navigate to *Run and Debug* tab in the left panel
2. Click on the dropdown to select the executable to run
3. Start debugging by clicking on the green play button

![Debugger Usage]({{site.baseurl}}/static/stock-photos/debugger-launch.png)


## Debugging Python

In this section, we will go over how to set up and use the debugger for the Python MPs. 


### Setup

To extend VS Code to support debugging Python programs: 

- Launch VS Code. Note that if you are on Windows, you **may or may not** need to [launch VS Code from WSL](https://code.visualstudio.com/docs/cpp/config-wsl#_run-vs-code-in-wsl) depending on the test cases you want to run
- Install the [Python extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
- If you haven't already, you should also `pip install pytest`, which is the testing framework we will use for the Python MPs


### Configure

Once you have the Python extension installed and your MP open in the editor, you will configure the test by doing the following:

1. Navigate to *Testing* tab from the VS Code Activity bar (left sidebar)
2. Click *Configure Python Tests*
3. When prompted, select *pytest* as the testing framework
4. Select the directory containing the tests

![Debugger Usage]({{site.baseurl}}/static/stock-photos/python-dbg-configure.png)

You should see all the test files with their test cases listed in the *Testing* tab. You can run and debug each individual test case or the entire test file, look at the output, etc. through the interface. 

![Debugger Usage]({{site.baseurl}}/static/stock-photos/python-dbg-interface.png)

For more information, please check out [Python testing in Visual Studio Code](https://code.visualstudio.com/docs/python/testing). 


## Useful Features

The visual debugger works pretty much like the command line version of GDB, where you can pause the execution of a program to examine its states and memory. Even if you have never used GDB before, the debugging interface provided by VS Code is fairly intuitive and easy to get around. 

Some of the useful features include: 

1. Adding or removing breakpoints by clicking on the red dot to the left of the line number. The program will pause at each breakpoint you set
2. Examining the state of the program when its execution is paused. For example, you can look at local variables, resgisters, function call stack, etc
3. Stepping into, over, or out of a function. Continue the execution until the next point. You can use these options to control the flow of your program (e.g. executing your code line by line) in order to see how your program states change
4. Using the *Debug Console* to execute any additional debugger commands you want. For example, `-exec info registers` will list registers in use

![Debugger Usage]({{site.baseurl}}/static/stock-photos/debugger-usage.png)