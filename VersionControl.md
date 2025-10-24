# Version Control

Using verion control is a must for replicability. Version control allows you to save and easily retrieve snapshots of your code. It is good practice to save snapshots of your code frequently during development. This makes it easier to track down bugs, do performance tuning, etc. For replication, it is essential to save a snapshot of the exact version used perform any experiments.

The most widely used version control system is [`git`](https://git-scm.com/). It is simple to use and there are many ways to use it.
 * On the command line,
 * Through an IDE, such as [VS Code](https://code.visualstudio.com/),
 * Through [Github](https://github.com) (file upload, CodeSpaces, etc.)

Getting started with git and using it locally is very simple.
```bash
~/path/to/project> git init
~/path/to/project> git add .
~/path/to/project> git commit -m "My first commit"
```
To hook up your local repo to Github, first create an empty repository on Github.

<img width="585" height="706" alt="image" src="https://github.com/user-attachments/assets/d89ee7af-3ccf-4b2d-99d8-42af4069dfbd" />

Then add it as a remote and push (you need to set up an SSH key first).
```bash
~/path/to/project> git remote add origin git@github.com:tkralphs/MyExample.git
~/path/to/project> git branch -M main
~/path/to/project> git push -u origin main
```
