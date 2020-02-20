---
layout: post
title: "Concurrent and Isolated Processes ."
description: "Blog Series on understanding Elixir/OTP"
author: Samuel Wasswa
category: Elixir
tags: Software
finished: true
---
This is the first post in our series about understanding Elixir/OTP. You can check out the [introduction]({% post_url 2019-10-21-understanding-elixir-otp %}) which also contains links to all posts in this series.

Elixir is highly scalable and fault tolerant because it leverages BEAM(Erlang Virtual Machine) concurrency. To appreciate the Erlang advantage, you must understand how Erlang's basic unit of concurrency, the process, handles multiple operations in an efficient way.

> A BEAM process is a concurrent thread of execution
> [ - Elixir in Action](https://www.manning.com/books/elixir-in-action-second-edition){:target="_blank"}

BEAM processes are distinct from OS processes, they are lightweight and are handled by the VM. In fact the entire VM runs in a single OS process. An Elixir app has a lot of processes running simultaneously. In fact, all Elixir code runs in processes. For example, below were the number processes running on my machine.

    iex(1)> Process.list |> Enum.count
    203

So you may ask what all these processes are doing. A simple way to find out is to use a tool called `observer`. This is an erlang module we can start in an `iex` session.

    iex(2)> :observer.start

![Observer]({{ site.url }}/assets/elixir/observer.png "Observer GUI")

You will see all the processes running in the Erlang VM from the processes tab. In my case, am running a Phoenix application so there are some processes related to the Database connection and the web server. You also notice that the observer started its own processes. Elixir modules in the VM will be prefixed with `Elixir.` The rest are erlang modules. This is just a small snippet. There are many more processes running which I cannot show for brevity.

The `observer` enables us to see everything that's happening in the Erlang VM. This is why the VM can be thought of as mini operating system for your code. The `observer` doesn't stop there, we can also interact with running applications. So for example if we have a process that is problematic, we can kill it.

Processes are identified by their PIDs (Process identifiers). So if I wanted to kill a process I would identify the PID first.
Lets take a contrived example of an `iex` session.

To get the PID of my `iex` session, I call `self`

    iex(3)> self()
    #PID<0.3631.0>

I would then start the `observer` and look for the PID right click on the process and kill it as shown below. Neat!

![Kill]({{ site.url }}/assets/elixir/kill_process.png "Kill Process")

In this instance if you kill the process. It will be restarted by a Supervisor but thats another topic for another time. However, its important to note that killing the process doesn't affect other processes because they are isolated.

In a nutshell, an Elixir application is made of lots of isolated processes that run concurrently. Some of the processes are spawned automatically when the application starts and continue running for the lifetime of the application while other processes are spawned on demand and run for a relatively short amount of time.

The Erlang VM starts a scheduler which runs the processes concurrently on the CPU.  However,running concurrently doesn't mean that all the processes are running at the same time. The scheduler actually allocates a slice of CPU time to a process. These slices of time are very small. When the time is up the scheduler "preempts" or "suspends" the running process and allocates time to another process. This is repeated multiple times for all processes with each process getting its turn.

The scheduler switches between processes so fast that they appear to be running simultaneously. This is also known as **Preemptive multitasking/scheduling**. The advantage here is no process will tie up the CPU. Every process will get its fair share.

It gets better because computers today have multiple CPU cores and to take advantage of these the VM creates a scheduler thread for each CPU core. So, a four core computer will have four schedulers and each of those schedulers will be running multiple processes concurrently. This means that at any given point in time four processes are running at the same time i.e in **parallel**.

I hope you can appreciate how Elixir utilizes the Erlang VM to achieve concurrency and parallelism. The VM does impressive stuff behind the scenes with its schedulers and we get to take advantage of this for free without complex language constructs. Our applications almost scales naturally without having to do anything special.

Next we will look at how these processes communicate with messages but if you want to get a different perspective to reinforce your knowledge about concurrency and parallelism, check out this[ post ](http://nathanmlong.com/2017/06/concurrency-vs-paralellism/){:target="_blank"}.

I will leave you with this quote.

> **Concurrency vs. parallelism**
>It’s important to realize that concurrency doesn’t necessarily imply parallelism. Two concurrent
>things have independent execution contexts, but this doesn’t mean they will run
>in parallel. If you run two CPU-bound concurrent tasks and you only have one CPU core,
>parallel execution can’t happen. You can achieve parallelism by adding more CPU cores
>and relying on an efficient concurrent framework. But you should be aware that concurrency
>itself doesn’t necessarily speed things up.
>[ - Elixir in Action](https://www.manning.com/books/elixir-in-action-second-edition){:target="_blank"}.



Side note: The `observer` tool is a great a way to visualize what's happening in the Erlang VM. You will begin to appreciate the concept that the VM is a mini operating system for your code with multiple cooperating processes. You can find more information in the [ Observer's User Guide](http://erlang.org/doc/apps/observer/observer_ug.html){:target="_blank"}.









