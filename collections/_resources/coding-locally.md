---
title: Coding Locally
---

# Virtual Machine

Your CS 240 virtual machine is only equipped with a shell.  There are several built-in text editors that allow you to edit files directly on your VM.  These include:

- `nano`, the "Notepad" of Linux (quick, simple, easy, no frills)
- `vim`, the "swiss army knife" of Linux (can do absolutely everything, if you know how to use the tool)
- `emacs`, another "swiss army knife" of Linux (just as functional, but completely different)

All of these tools are simple compared to what's possible with modern IDEs.


# Guide: Programming Locally, Connecting Remotely

Many modern IDEs provide a plugin system to extend the functionality of the IDE.  A common plugin on all modern IDEs includes the ability to program locally while having your code "save" or "sync" to a remote computer.


## Visual Studio Code

In our meetup session, Visual Studio Code ("VS Code") was the clear winner of favorite IDE.  VS Code is a free, open-source IDE that's developed by Microsoft that works as a general-purpose IDE.  This means that:

- VS Code will open all types of files from all types of programming languages,
- VS Code has plugins to provide syntax highlighting, auto-complete, documentation, and to hundreds of different programming languages,
- You can customize VS Code to be your dream IDE

If you have never used VS Code, I encourage you to try it out.  Download and install VS code from [https://code.visualstudio.com/](https://code.visualstudio.com/).


### Setting Up VS Code for Remote Work

#### Guide: Using the SFTP Extension

1. **Install the Extension** -- In the "Extensions" tab (Ctrl/Cmd + Shift + X), search for `SFTP` and find the extension by `liximomo`.

2. **Open Your Course Folder** -- Open your `cs240/netid` github folder that contains a clone of your course git repository in Visual Studio code.  (You can usually do this by either choosing `File -> Open Folder` in VS Code or, on Windows, by right-clicking on the folder and choosing "Open with Code" inside of File Explorer).  Changing settings now will apply these settings only to this "project".

3. **Configure the Extension** -- After the extension has been added and your VS Code is open in your course folder, use the Visual Studio Command Palette (Ctrl/Cmd + Shift + P) to search for `SFTP` and choose `SFTP: Config`.  In the configuration:

    - Find the key `"name"` and modify the value to be something descriptive (for example, `"CS 240 Virtual Machine"` ),
    - Find the key `"host"` and modify the value to be the hostname of your VM (you can find this in your Compass 2g gradebook if you're not sure of it),
    - Find the key `"username"` and modify the value to be your NetID, and
    - Find the key `"remotePath"` and modify the value to be `/home/netid` (replacing **netid** with your NetID), and
    - *(Optional, less secure)*: Add a new key `"password"` and add the value to be your password (this will be in plaintext, so be careful)

4. **Set up the Synchronization** -- Use the Visual Studio Command Palette again (Ctrl/Cmd + Shift + P) to search for `SFTP` and choose `SFTP: List`:

    - Follow the dialogs to choose your connection, enter your password, and then choose the location on the virtual machine where you want to sync.
    - You're all sync'd up!  Files you save inside of Visual Studio code will sync with your VM. 🎉

5. **Use the Terminal for SSH** -- Finally, you can also get a terminal window within VS Code that's on your VM.  Using the Command Palette, find `SFTP: Open SSH in Terminal` to open an SSH connection in the VS Code terminal.
