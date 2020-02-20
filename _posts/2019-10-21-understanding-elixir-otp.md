---
layout: post
title: "Understanding Elixir/OTP - Introduction ."
description: "Blog Series on understanding Elixir/OTP"
author: Samuel Wasswa
category: Elixir
tags: Software
finished: true
---
Elixir shines when you are looking for concurrency and fault tolerance in your applications mainly because it runs on the Erlang VM which has been battle tested for decades by running stable and highly concurrent systems in telecoms. This is where the term OTP(Open Telecom Platform) came from though its now a more generic term for a framework that enables applications leverage Erlang's concurrency, distribution and error handling capabilities.

In this series, I aim to explore OTP as it relates to Elixir and how you can utilize it in your own applications. I will try to provide a general overview of these topics.
* [Concurrent and Isolated processes]({% post_url 2020-02-14-concurrent-isolated-processes %})
* Sending and Receiving Messages
* Asynchronous Tasks
* Stateful Server Processes
* OTP GenServer
* Linking Processes
* Fault Recovery with OTP Supervisors

Hopefully, after covering these topics you will gain a new appreciation of how Elixir handles concurrency and fault tolerance.

These articles are inspired and based on the awesome [Pragmatic Studio Elixir Course](https://pragmaticstudio.com/courses/elixir). I strongly recommend that you take this course if you are just getting started with Elixir.
