---
layout: post
title: The king is dead...

date:   2019-07-21 20:00
description:  The Thorntail project short life comes to the end. Why it happened and what are the alternatives in my opinion?

comments: false

tags:
- java
- development
- microservices
- thorntail
---

In the last month, I have written 2 articles about [Thorntail](https://thorntail.io/). I'm working on a new project and I need an application server for it. And Thorntail was ticking all boxes. It is well suited for a microservices architecture. It produces a jar file with an embedded application server. It bases on a WildFly application server, which we are already using and have knowledge about. It is an Open Source project with RedHat behind it. It keeps up with implementing the [Microprofile](https://microprofile.io/) standard, supporting its latest version. It has a reach library of fractions, which extends its functionality, the concept I love. The only weak part of the project is its documentation. I found it very minimalistic, especially for fractions.

I have a winner and I've started playing with it. I [make it works with JRebel](/2019/06/how-to-use-jrebel-with-thorntail) and [enabled HTTPS](/2019/06/how-to-enable-https-in-thorntail). It integrates without problem with [Vaadin](vaadin.com). Thanks to [Microprofile JWT-auth](https://microprofile.io/project/eclipse/microprofile-jwt-auth) I was able to use JWT tokens for user authorisation. So far so good... and then I found this blog post - [Thorntail Community Announcement on Quarkus](https://thorntail.io/posts/thorntail-community-announcement-on-quarkus/). The post changed everything and in my honest opinion, it should be pinned to the project home page. Why it is so important? It says that Thorntail is dead, or in the best case, it is in an agony which would finish in November 2020. That means no one should consider Thorntail as an option anymore. And those who are using it, should start migrating it into an alternative framework. But why Red Hat decided to kill the project, which has everything to succeed in a microservices age?

# To change or not to change the name?

I guess, there were many factors which contributed to the current state of the project. But for me, a critical mistake was a name change from WildFly Swarm to Thorntail. This decision took out momentum from the project. Instead of improving the product, they had to change its name everywhere. It's easy you say? Find all references to the old name and replace it with a new one. But there are much more than that. The example of [Jakarta EE](https://jakarta.ee/) (former Java EE) shows how hard it is to minimise the damage caused by the name change. But, Eclipse Foundation had no choice, due to Oracle rights to Java. In the case of Thorntail, this was the project leadership decision.

One problem related to the name change, which I would like to highlight, is knowledge. It made all related blog posts, StackOverflow questions, or forum threads out-of-date. Imagine a newcomer, who has no idea about the name change. He/she has some common problem and there is a nice blog post which explains how to fix it. But the blog post is about the problem with WildFly and not Thorntail, even if both projects are the same. It doesn't help that Thorntail documentation is not very extensive. It also doesn't mention how the project relates to the WildFly Swarm. To illustrate the problem, I have googled `Torntail framework` and `WildFly Swarm framework` phrases. The first query returns ~13k results and the second ~190k.

# ... long live the king(?)

The post which announced the death of Thorntail tells a different story. It says that Red Hat found [Quarkus](quarkus.io) superior to Thorntail in terms on performance. As such Red Had decided to put most of Thorntail resources into Quarkus. And yes, Quarkus is very promising, but is it ready to replace Thorntail yet? I mean, is it production-ready? [It looks like no](https://quarkus.io/faq/) as the project is still in beta. I am following the progress of Quarkus with excitement, and I would love to use it in my project.  But can I accept the risk of using it in the production? I still have a couple of months before I have to make the final decision. In other circumstances it would be an easy one. Of course, there are alternatives to Quarkus, such as [Payara](https://www.payara.fish/), [Open Liberty](https://openliberty.io/), or [Apache TomEE](http://tomee.apache.org/apache-tomee.html). If no Quarkus then I need choose one of them, but they are not as appealing to me as Thorntail was.
