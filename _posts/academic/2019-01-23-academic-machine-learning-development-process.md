---
layout: post
title: Academic Machine Learning Development Process
category: academic
tags: [ml, vscode]
---

Machine learning projects nowadays cannot easily fit into a laptop. While we optimize laptop for maximum portability, it does not usually come with a powerful GPU. The conventional wisdom is that we buy our own machine learning rig with powerful enough GPU(s). This now splits our development machine, where we actually writing codes, and the running machine, where we have all the libraries and resources. Of course, In this scenario, we don't include the GPU farm, if you have any, in that case other layer of considerations must be in place. 

Splitting developing machine and running machine give rise to a lot of headaches because the latency of the task is generally very low, iterations are fast. How could we manage to do this? Worse yet, usually during coding we want to have access to all development tools including but not limited to code suggestion and auto-completion. If all the libraries reside in the running machine, how could we have a precise code suggestion then?

I think **[Jupyterlab](https://github.com/jupyterlab/jupyterlab)** (and all other IDEs with client side UI) try to solve the right problem, it is really a problem developers haven't seen conventionally, it is just beginning to emerge and our tools are not capable of it yet. Jupyterlab is quite limited by itself. The project began as a language agnostic platform, it does not provide any language specific code suggestion and completion. This task should be done via extensions according to the project philosophy. The problem is rather the project is still very young and there is no such extension. This renders the tools only good enough for small code bases where the interactivity which is the main-selling point of this tool outshines its flaws.

We now move on to see other candidates where the core resides on the running machine but has a client-side UI. The solution I have found is quite unexpected, it is [**Visual Studio Code**](https://code.visualstudio.com/). I don't think Code is designed to be a server side IDE at all (only recently it seems to have enough requests to do so), but since the introduction of its feature [**Live share**](https://code.visualstudio.com/blogs/2017/11/15/live-share), it now has the capability to do so. 

Live share solves our problem in a sense that we can remote to our running machine and edit the code there while maintaining laptop portability. If all operations are done in the server side e.g. file operations, debugging, versioning, leaving only the visuals and controls to be transmitted through the wire, the cost of being remote should be rather small since these operations are not latency sensitive. If the dynamics of GUI is known in the client side, there should be no delay between typing and presenting the characters typed. 

Live share is still beta though and only recently it has met the usable quality. It still has a long way to go i.e. versioning is not fully supported from the client but we still can use a separate terminal to do that, not that hard.

To run live share we need to run Code, and to run Code we need a desktop environment. The fact that Code does not support headless mode is still a caveat to use this solution, but for me this is rather slim since I could just install a **[VNC](https://en.wikipedia.org/wiki/Virtual_Network_Computing)** server of some kind (I personally use **[TurboVNC](https://code.visualstudio.com/blogs/2017/11/15/live-share)**) and run the Code from there. 

Visual Studio Code now seems to focus on machine learning community as well, you can see with its powerful **[Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)** extension, and also its **[Intellicode](https://marketplace.visualstudio.com/items?itemName=VisualStudioExptTeam.vscodeintellicode)** extension which supports many deep learning frameworks.