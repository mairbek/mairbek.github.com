---
layout: post
title: "Programs Should be Small"
date: 2013-02-02 12:03
comments: true
categories: Programming, Development, Architecture
---

## Evolution of software matters

How big is the source code you are working on? How many developers do you have in your team? If you are going to add new feature tomorrow, how fast will you be able to do that?

Maintainability matters. Well, it matters in case if you're willing to build a successful applications evolving over the years. And if you would like to be able to quickly add new features you definitely want to see the developers spending most of the time on writing code and hacking on a new stuff.

Working mostly on enterprise projects I used to see multi-developer teams working on a single gigantic piece of software, usually built using one particular technology. This turns out to be inefficient and leads to unmaintainable code. A developer on a such project is forced to spend most of his time on trying to understand the way the current system works instead of adding concrete value. And I'm sorry, there is no value in reading pages of purely written legacy code. The only real value programmer can add is a new code.

Another disaster is the verification of the produced code. Getting instantaneous feedback by executing a newly written code is the essential part of the developer's productivity. Unfortunately running enterprise software is usually a real pain. You are forced to use rare application servers, monstrous databases, and in addition you have huge codebase with everlasting build time.

## Decomposition for the win

Increasing program-to-developer ratio is the way of building things that keeps the developers productive and happy. Instead of having hordes of programmers working on one single codebase, split your system to a small programs that could be deployed independently. Beautiful reusable code should be extracted as a libraries and be maintained independently. [Open source that libraries](http://tom.preston-werner.com/2011/11/22/open-source-everything.html) if possible and you'll get the feedback from the peers.

Writing small programs or services, if we talk about web apps, benefits in long term perspective. First of all small services are easy to reason about. The key is to stick with [open closed principle](http://en.wikipedia.org/wiki/Open/closed_principle), write services that do one thing, and do it well. It's easy to execute these services independently and see whether they work or not. For example for Gmail you might extract a service responsible for sending and receiving emails, you might have a separate service that does spam filtering, separate service for storing e-mails, a service that can apply filters and a standalone web-app that communicates with these services.

One big advantage of splitting you program is that you are no longer restricted to use one particular platform. We know that _ruby_ or _node.js_ make you really productive in writing web apps but their performance is pretty inefficient comparing to _C++_ or _Java_. Since we are able to deploy services independently we can forget about monolithic _Java EE_ and combine different technologies and for example build a web app with _rails_ and backend services using _C++_ and use some convenient protocol like [Protobuf](http://code.google.com/p/protobuf/) or [Apache Thrift](http://thrift.apache.org/) for communication.

Truth been told decomposition of the system is somewhat non-trivial. One thing is becoming obvious, well known layered architecture is going to ruin maintainability of your project. It's tempting to stick with classical enterprise approach and create a service for accessing the database, service that encapsulates caching, service that does business logic and a presentation service. But features are cross-cutted usually through these layers. So any new sophisticated feature will require changes in every layer. In addition most of the code will be responsible for converting messages from one format to another.

Software design and development is a pretty sophisticated activity and doesn't fully depend on how smart you are and how many books you've read. It's just so many thing you have to care about in order to achieve good quality solution. Keeping programs small is a great simple tool that allows you to reduce complexity of the solution and results in clean design and improved developers productivity.
