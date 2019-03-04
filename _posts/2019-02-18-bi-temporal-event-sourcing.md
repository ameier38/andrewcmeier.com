---
layout: post
title:  Bi-Temporal Event Sourcing with Equinox
cover: /assets/images/bi-temporal-event-sourcing/cover.png
permalink: bi-temporal-event-sourcing
date: 2019-01-05 12:08:00 -0400
updated: 2019-01-06 08:06:00 -0400
categories: 
  - F#
  - event sourcing
  - Equinox
  - bi-temporal
comments: true
---

In this post, we will demonstrate how event sourcing 
can be used to model a bi-temporal domain. We will inspect
an app written in F# that leverages Jet.com's open source library 
[Equinox](https://github.com/jet/equinox).

## What is event sourcing?
Event sourcing is a way of modeling a system such that the state of the
system is determined by replaying a series of events. It focuses on
storing the events instead of storing the state.
> See the [Resources](#resources) section for more information on event sourcing.

## What is Equinox?
__Equinox__ is A unified programming model for event-sourced command 
processing and projections for stream-based stores. In other words, 
it is a collection of .NET libraries to help build event-sourced systems.

## What is a bi-temporal domain?
A bi-temporal domain is one in which two timelines are used to determine
state. Typically these timelines are the entity time and event time. This
is illustrated in the below image. 

![bi-temporal](/assets/images/bi-temporal-event-sourcing/bi-temporal.svg)

As events occur along the event timeline, they can be effective at a 
different date in the entity timeline. The state of the entity is determined
by the ordering the events according to the entity timeline and
replaying them against an initial state. The event timeline can then be 
used to get the state of the entity at any point in time by filtering 
which events should be included in the entity timeline. A good way to determine
if a domain is bi-temporal is to ask yourself if any events in the domain
can be applied retroactively.

## Lease API
In this post, we will inspect an API that models a lease (e.g., car lease).
I chose a lease because it is an imaginary thing, an agreement between parties,
and IRL the parties involved can decide to apply some change retroactively.

### Requirements
- A user should be able to create a lease, modify a lease,
schedule a payment, make a payment, terminate a lease, and undo any action.
- A user should be able to apply any of the above actions in the past.
- A user should be able to get the state _as of_ any point in time, meaning
the state that includes all events, including retroactively applied events.
- A user should be able to get the state _as at_ any point in time, meaning
the state that only included events that occurred at and prior to that point
in time.
- A user should be able to audit all the actions that have occurred.

### Dependencies
- [`dotnet` CLI](https://andrewcmeier.com/win-dev#dotnet)
- [FAKE](https://andrewcmeier.com/win-dev#fake)
- [Docker](https://andrewcmeier.com/win-dev#docker)



## Resources
- [Getting started with FAKE](https://fake.build/fake-gettingstarted.html)

Leave a comment below if you have any questions and I will try my best to answer!
