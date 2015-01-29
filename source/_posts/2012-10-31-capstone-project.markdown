---
layout: post
title:  "Capstone Project"
date:   2012-10-31
categories: capstone
---
##Introduction
Our Capstone project is to simulate a functional enterprise computer network in a virtual environment.  The computer network will involve an integrated Microsoft Windows server and Linux server setup which will include auto-deployment of each clients.

##Authentication
A secure, centralized authentication system is the Holy Grail of systems administration in a heterogeneous network.   

##PXE Pre Execution Environment
The Preboot eXecution Environment (PXE, also known as Pre-Execution Environment; sometimes pronounced "pixie") is an environment to boot computers using a network interface independently of data storage devices (like hard disks) or installed operating systems.

##Puppet
Puppet is IT automation software that helps system administrators manage infrastructure throughout its lifecycle, from provisioning and configuration to patch management and compliance. Using Puppet, you can easily automate repetitive tasks, quickly deploy critical applications, and proactively manage change, scaling from 10s of servers to 1000s, on-premise or in the cloud.

Puppet is available as both open source and commercial software. You can see the differences here and decide which is right for your organization.

How Puppet Works
Puppet uses a declarative, model-based approach to IT automation.

  - Ordered List ItemDefine the desired state of the infrastructure’s configuration using Puppet’s declarative configuration language.
  - Simulate configuration changes before enforcing them.
  - Enforce the deployed desired state automatically, correcting any configuration drift.
  - Report on the differences between actual and desired states and any changes made enforcing the desired state.

In short, the only way to do away with scripting is to create an application that abstracts the scripting process. Automated scripting would then be based on user descriptions of the desired outcome/goal rather than having the user describe the steps to reach said goal.

This really gets to the heart of how things need to change. I wouldn't call it "abstracting the scripting process" any more than I'd call a web browser "abstracting the network," but that doesn't make it inaccurate.

What is exactly right is his characterization of the ideal state: user descriptions of the desired outcome/goal. We call this approach model driven. What that means is administrators can describe how a system should look, and let tools move the system from whatever state it's in to the end state automatically.

This distinction between model driven and script driven has been evolving for a while. Tools like Cfengine and Puppet have supported model-driven configuration for years (the Puppet web page describes itself as having a "declarative language"). They give administrators a language to (for example) specify the DNS server for a machine; the tools are responsible for knowing how to update the right files on the system in a safe manner. Before Puppet and Cfengine a simple tool called rdist was state of the art for managing distributed systems, and it was entirely script based. You don't see it much anymore; rdist scripts are much more brittle than the more popular model-based approach.

It's probably obvious why I like this approach better than writing scripts for each change:

Models are understandable. It's much easier to look at a model and know that it's correct than to look at a script which implements the model.
Models can be verified and have policies enforced against them.
Models have a much simpler testing matrix.
Models are invertible; just move back to the old version of the model.
A model-based approach supports a high level of rapid change and automation.
All of this assumes you have a reliable way of applying the model to a production machine”but once you do, it works over and over. You're splitting the what from the how. It's exactly the same idea which lies beneath the Model-View-Controller paradigm, and it's right for exactly the same reasons.

When I was speaking with a prospective customer last week they described the same idea using slightly different language. They described their goal as being able to completely describe the "goal state" of the system and have "viewers" which could see how a system differed from the goal state. "Controllers"would be responsible for moving a system to its goal state. This is exactly the same idea as model-based system management, just using different language (I'd just about finished reading Neal Stephenson's book Anathem, so I obviously found the idea of systems moving between points in a Hemn space quite appealing!).

Model-based approaches are a compelling alternative to scripts. It's the right approach for managing large numbers of machines without employing large numbers of people.