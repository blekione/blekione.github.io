---
layout: post
title: How to use JRebel with Thorntail and Eclipse IDE

date:   2019-06-19 21:00
description: A step by step guid how to enable JRebel in a Thorntail service.

comments: false

tags:
- java
- development
- microservices
- thorntail
---

JRebel is a tool which updates an application after changes (e.g.: in code) without redeploying/restarting it. I’m using it at work, where we deploy an application to a WildFly server and it saves me a lot of time. How much? My JRebel statistics say that,

>Over the last 240 days Jrebel prevented at least 7028 redeploys/restarts saving you about 285 hours

Recently I’m learning [Thorntail](https://thorntail.io/), which I might be using for a new project. After I made it run as a simple service I was looking for a way to use JRebel with it. Unfortunately, I couldn’t find any guide on how to do it and this post tries to fill that gap.

The Thorntail service runs as a standalone jar. For example, when packaged as [a hollow jar](https://docs.thorntail.io/2.4.0.Final/#hollow-jar_thorntail) it can be run with the command

{% highlight bash %}
java -jar myapp-hollow-thorntail.jar myapp.war
{% endhighlight bash %}

To integrate JRebel with Thorntail below steps are required:

1. Create/generate `rebel.xml` configuration file.

2. Set the `-agentpath` run time argument.

3. Enable automatic project rebuild.


# Create rebel.xml configuration file

The first step is to generate a `jrebel.xml` file and add it to the project. In basics, this files tells JRebel agent the location of files which should be monitored for changes.

Eclipse IDE has a JRebel plugin which:

* Installs JRebel and then prompts you for updates to the newest version.

* Generates `rebel.xml` file upon enabling JRebel in a project.

* Provides JRebel configuration view.

As mentioned above, the `rebel.xml` file is generated when enabling JRebel for a project and the default configuration should be enough to make it work.

If you don’t want to install the plugin, there are other options to create a `rebel.xml` file. You can [Maven](http://manuals.zeroturnaround.com/jrebel/standalone/maven.html) or [Gradle](http://manuals.zeroturnaround.com/jrebel/standalone/gradle.html) plugin for this purpose or [create the file manually](http://manuals.zeroturnaround.com/jrebel/standalone/config.html).


# Set the -agentpath run time argument

The second step shows how to set a JVM to use a JRebel library to detect changes and to “update” the code on the fly, without the need of restarting the service.

The Thorntail service is a standalone jar. In this case, JRebel Java agent is used to instrument JVM to listen for code changes. This is done by adding `-agentpath` argument with the JRebel agent location to run the command.

{% highlight bash %}
java -agentpath:<path_to_agent> -jar myapp-hollow-thorntail.jar myapp.war
{% endhighlight bash %}

## Use Eclipse JRebel configuration view to find the agent location path:

1. Select **_Help > Jrebel > Configuration > Startup_** to display the view,

2. Select **_Run locally from command line_**

3. Select JVM (e.g: 64-bit JVM) and target environment such as SpringBoot 2.x.

4. Copy the run command for a standalone jar and change it with paths to the service runnable.

The advantage of this solution is that the plugin will prompt you if a new version of JRebel is available and installs it for you. As such the run command from configuration file will always point to the latest version of the library. 

The JRebel can be also downloaded from [JRebel website](https://manuals.zeroturnaround.com/jrebel/standalone/install.html). In such a case `-agentpath` is a path to an installation location of the agent for a specific operating system. For example, for the `myapp` service, run on Ubuntu, if JRebel is installed in `/usr/lib/jrebel`, the run command will be:

{% highlight bash %}
>java -agentpath:/usr/lib/jrebel/lib/libjrebel64.so -jar myapp-hollow-thorntail.jar myapp.war
{% endhighlight bash %}

# Enable automatic project rebuild

At this point, when the service is started, JRebel banner should be added to the service logs. It means the JRebel agent is listening for the changes in your code. Now, every time the project is built with some changes, they should be added to the service without a restart.

JRebel doesn’t pick changes when they are saved, only after the project is rebuilt, for example with `mvn package`. But this is a manual process, which I had to eliminate. It took me a few hours of research and struggle to sort it out. To be fair this information is in JRebel manual, but it is difficult to find it if you don’t know what you are looking for. The missing piece was to enable automatic project rebuild in Eclipse (**_Project > Build Automatically_**).

And that is it. Now every time you make a change in the code and save it you should see a new log entry, such as:

{% highlight bash %}
>2019-06-18 00:16:11,742 INFO [stdout] (rebel-change-detector-thread) 2019-06-18 20:16:11 JRebel: Reloading class 'com.blekione.myapp.Demo'.
{% endhighlight bash %}
