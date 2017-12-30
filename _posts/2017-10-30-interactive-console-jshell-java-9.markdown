---
layout: post
title:  "JShell: using Java 9 interactive console"
img: posts/jshell-java-9-repl-interactive-console.png
categories: java, programming, repl
tags: [java, jshell, console, java9]
---

One of the nice news of Java 9 is the introduction of a REPL (Read-Eval-Print-Loop): `JShell`
Let's take a look at how to use it.

## Running JShell
If you have the latest version of the JDK installed just run `/bin/jshell`.

## Running with Docker
If you don't have Java 9 installed, you can run it using Docker:

`docker run -it openjdk:9-jdk /bin/jshell`
Optionally, you can create an alias of this command in your `~/.bashrc`, to avoid running it each time.

## Useful commands

Taking a look at variables you declared:
```
/vars
```

Listing resources you typed:
```
/list
```

Opening a file:
```
/open filepath
```

and, very important, to exit:
```
/exit
```

For the entire list of available commands run: `/help`

For more info and live demo take a look at [this good video](https://www.youtube.com/watch?v=0RegttLUXeU) by Miro Cupak @JavaOne conference.