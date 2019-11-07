---
layout: post
title: Functional Agent
cover: /assets/images/win-dev/cover.png
permalink: functional-agent
date: 2019-12-04 12:00:00 -0400
updated: 2019-12-04 12:00:00 -0400
categories: 
  - F#
  - MailboxProcessor
  - Agent
  - Asynchronous
comments: true
---

Happy F# Advent! This post demonstrates how to use
F#'s MailboxProcessor to create agents to manage
asynchronous workflows. We will create _functional_
agents in the sense that we will use functional
programming techniques such as immutable data
structures and build-ins such as `fold` to maintain
state.

## Agents
An agent is a process which asynchronously processes messages.
Agents can maintain an internal state and they may provide
mechanisms for communicating with callers.

F#'s has a native implementation of an agent called a MailboxProcessor.

## Setup
First install [Docker]() and [Docker Compose]().
If you just want to run the application then this 
is all that is needed and you can skip to the next section.

To build the application install the [.NET Core SDK]().

Confirm you have the installed it correctly.
```shell
dotnet --version
3.0.100
```

## Running the Typewriter
Typewriter usage.
```
USAGE: Typewriter.exe [--help] [--rate-limit <int>] [--parallel-limit <int>] [--buffer-size <int>] [--file-path <string>]

OPTIONS:

    --rate-limit, -r <int>
                          specify the limit on the number of keys pressed per second
    --parallel-limit, -p <int>
                          specify the number of keys to process in parallel
    --buffer-size, -b <int>
                          specify the number of keys to process before writing to console
    --file-path, -f <string>
                          specify the path of the file in which to write buffer
    --help                display this list of options.
```

Run the application using Docker.
```
docker-compose run --rm typewriter -r 2 -p 2 -b 10 -f test.txt
```
> This runs the typewriter application with a rate limit (`-r`)
of 2 keys per second, a parallel limit (`-p`) of 2,
a buffer size (`-b`) of 10, and writes to the `test.txt` output file.



