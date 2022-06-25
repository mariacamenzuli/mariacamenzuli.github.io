---
layout: post
title: Improve the User Experience in your Custom Dev Tools
author: Maria Camenzuli
---

Whether you are writing an internal tool for your colleagues at work, or writing something open source, you should be thinking about the user experience other developers have with your tool. Just like in a client facing app or API, a good user experience in dev tools is incredibly valuable, and requires deliberate thought and consideration to get right.

Over the past few months, I've been doing a lot of work on dev tooling. My team at work currently produces and maintains a set of Gradle plugins that automate our software packaging and versioning processes. We are also involved in work done on deployment tooling, which is mostly Ansible complemented by bash and python scripting. Having gone through several iterations of these tools, and listening to feedback from my colleagues who use them, I am writing this article to share with you 3 simple things you can do to improve the user experience in your own custom dev tools.

## 1. Take a design first approach to user input.
For most non-trivial tools, you will most likely need input from your users. This input could be in the form of a configuration file, a closure in a buildscript, or even just arguments required when invoking a script. In any case, the way this input is specified is basically the API of your tool, and like in API design, I have found that a very good way to decide on what the user input should look like is to take a design first approach.

Before coding anything up for a new feature, ask yourself 'If I were using this tool to work on my own project, how would I prefer to use it? What would be the cleanest and simplest way to specify what I want this to do?' Take your time coming up with an answer, and try out different options. Once you've come up with a couple of alternatives, pick the one you think you would like best, maybe even ask for a second opinion from a colleague, and then figure out how you will write the code that accepts the input in your selected format. This way, you are much more likely to end up with a tool that is pleasant and intuitive to use.

As a simple example, imagine a script that can be triggered with some optional parameters. There's a good chance that if you do take a design first approach, you end up with a script invocation that looks something like the following.

`> my_script true 5`

However, if you consider how you want to use this script before hand, you might think it would be intuitive to specify the optional parameters with something like the following.

`> my_script --with-option-a --parameter-b=5`.

## 2. Write good documentation.
When you need help understanding how to use a bash command you bring up the manual pages, where you know you will find all the information you need. When you need help understanding what some Spring method does you take a look at the Spring javadocs, because you know you will find an explanation. Similarily, your colleagues, or any other users of your tool, should have a reliable source of information when they need help properly using and configuring it.

I personally found that a good readme for your tools will make life easier for both you and your users. It will help you because your colleagues will stop interrupting you to ask questions about how to use your tools, and it will help your colleagues because they will have quick access to the information they need.

But what is in a good readme? It's probably safe to say that most people do not want to read a giant wall of text when they're adding a plugin to their buildscript. Buildscripts are rarely what you want to spend most of your time on when working on a project. Neither do they want to sift through endless text to find out how they can use your deploy script to deploy their hotfix when a critical bug is on production. Therefore I would suggest as much simplicity as possible ([KISS](https://www.wikiwand.com/en/KISS_principle) is not just for code you know). Nothing beats a couple of code snippets showing example usages or configurations that cover the majority of use cases. You can supplement that with some brief sentences or paragraphs indicating how the examples can be customized to meet different needs. If the document is long enough to require scrolling through it, consider a clickable table of contents.

## 3. Print out helpful error messages and logs.
Your dev tools should handle errors as gracefully as possible, giving the user helpful information about what went wrong. Remember that dev tools are there to enable users to be more productive. If you are writing a Gradle plugin, for instance, your users are developers trying to setup their buildscript. If something goes wrong in their build, they are going to need to know what happened so they can fix it. In particular, try to identify errors that can happen because of misconfiguation, or in relation to some other user input, and make sure you are printing out a helpful error message explaining what needs to be corrected in order for the tool to work. You don't want your colleagues to have to try to figure out what went wrong by going through a stacktrace of your code, or worse, come disrupt whatever you're currently working on to ask how to fix their broken builds. That's not going to help anyone's productivity.

Apart from error messages, your tools might also output some logs indicating what work is being done. It will be helpful to your users if you are logging only relevant information at the right level, so that you do not clutter logs. For Gradle plugins, for instance, logs at the `lifecycle` level should include any information you think would be useful in a standard successful build run. These tend to act as a progress indicator for your tasks. Logs that give deeper insight into the specifics of what the plugin is doing should be marked as `debug`.

## Conclusion
A good user experience is as important in your dev tools as it is for your typical client facing applications. In fact, as a developer of dev tools, it helps to consider your colleagues and any other user of your tool as your client. This way you can deliberately start considering the experience they have when using your tool, and what you can do to maximise their efficiency and happiness while using it.

A good dev tool gets the job done. A great dev tool gets the job done while causing no headaches, being intuitive to use, and providing enough information to keep the user as productive as possible.
