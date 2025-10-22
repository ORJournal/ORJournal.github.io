# Tutorial Materials from the *Workshop on Replication at INFORMS Pubs*

This tutorial will cover the basic of putting together a high-quality replication package.
 * Tools for making it easier to replicate results.
 * What to include in the replication package.
 * How to organize the replication package. 
 * How to write a complete and informative README.

## Table of Contents
 * [Tools](#tools)
   * [Version Control](#version-control)
   * [Build and Installation](build-and-installation) 
   * [Environment Management](#environment-management)
   * [Scripting](#scripting)
 * [Replication Package](#replication-package)
   * [Contents](#contents)
   * [Organization](#organization)
 * [README](#readme)

## Tools

### Version Control

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

### Build and Installation

Most projects these days are written in high-level languages that do not require compilation. This simplifies things, but it can still be complicated to package your software in a way that makes it easy for another person to install in on their computer from scratch. 

#### C/C++

For codes written in C/C++, it is almost always necessary to provide either a hand-generated Makefile or a CMake setup to make building easy. Making C/C++ codes portable is difficult, so ensuring the code builds on Linux is probably the lowest common denominator.

#### Python



#### Julia

#### R

### Dependency Management

 * [Jupyter Notebooks](https://jupyter.org)
   * [Binder](https://mybinder.org)
 * [Docker/Podman](https://docker.com)
 * [Nix](https://nixos.org/)
 * [Conda/Mamba](https://docs.conda.io/en/latest/)
 * [Homebrew/Linuxbrew](https://brew.sh)
 * [Github](https://github.com/)
   * CodeSpaces
   * Actions

### Scripting

#### Bash

#### Julia

#### Python

#### Jupyter Notebooks

## Replication Package

### Contents

### Organization

## README

