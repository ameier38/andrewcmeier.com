---
layout: post
title:  How to Jekyll
date:   2018-10-27 11:37:39 -0400
categories: 
  - jekyll
  - windows
  - blog
  - markdown
comments: true
---

This post will walk through the steps necessary to build
a blog using GitHub Pages and Jekyll on Windows.

## Environment Set Up
Checkout the [Windows Development Environment post]({{ site.baseurl }}{% post_url 2018-08-16-win-dev.md %})
for a guide on setting up a Windows development environment. 

1) Install Scoop.
  Scoop is a command-line installer for Windows

  1) Open PowerShell.
  2) Set the execution policy.
      ```
      > set-executionpolicy remotesigned -s currentuser
      ```
  3) Install scoop.
      ```
      > iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
      ```
  4) Add extras and versions buckets.
      ```
      > scoop add bucket extras
      > scoop add bucket versions
      ```

2) Install [Git](https://git-scm.com/)
  Git is a version-control system for tracking changes in computer files
  ```
  > scoop install git
  ```

2) Install [Ruby](https://www.ruby-lang.org/en/).
  Ruby is a dynamic, open source programming language with a focus on simplicity and productivity.
  ```console
  > scoop install ruby
  ```

3) Install [MSYS2](https://www.msys2.org/)
  MSYS2 is a software distro and building platform for Windows.
  ```console
  > scoop install msys2
  > msys2
  ```
  > A console will open. Once it finishes running
  commands just close the console.

4) Install the MSYS2 and MINGW development toolchain.
  ```console
  > ridk install 3
  ```

5) Install Jekyll
  ```console
  > gem install bundler jekyll
  ```

## Create Your Site Repository

1) Create a new repository for your site.
  ```console
  > jekyll new my-site
  ```
  > This bootstraps a new Jekyll site in the directory `my-site`.

2) Initialize Git. 
  ```console
  > cd my-site
  > git init
  ```

3) Create a new empty repository on GitHub.
  ![create_new_repository](/assets/create_new_repository.png)

4) Copy the repository link.
  ![clone_repository](/assets/clone_repository.png)

5) Add the repository link as a remote.
  ```console
  > git remote add origin <copied link>
  ```

{% include disqus.html %}