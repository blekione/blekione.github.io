---
layout: post
title:  "The beggining"
date:   2018-02-23 17:24:53 +0000
categories: jekyll update
---

My name is Marcin and I'm a Software Developer. Welcome to my blog which will be a journal for my new project - a homelab.

>(define homlab box)

 The idea is to build my own kubernetes cluster for learning purpose. Currently at work I'm working on a project which is build around kubernetes (k8s) which is new technology for us and I have an opportunity to learn k8s which is a lot of fun. But I am limited by the scope of the project (not surprise) and I want to "go wild" and try things which probably won't have a use in our project. 
There are many other places over the internet which show how to build a kubernetes cluster, why I then decide to create new blog about it? There are few things which I think will be unique to my blog and one of them is that the server which will host the cluster must be bulid by myself. No I won't be using any AWS or any other cloud provider. I will start with bare metal server and I would need to set up everything way up. That would be interesting ride which keeps me excited.

> A nice blockquote
{: title="Blockquote title"}
{% include note.html content="
**Disclaimer** I have little to none experience with servers and building networks. There will be many trials and errors, but all fun is just about this - to create something and learn at the same time. I hope some other beginner homelabers will find this blog useful.
" type="danger" %}

**How** this all started?

The idea to buy own server came from OnenJDK infrastructure session at last LJC Open Conference, when one guy mentioned that he found cheap server on ebay and make others excited about the deal. I was already started learning kubernetes, which need at lest 2 nodes to form cluster. Quick calculation of the cost to set kubernetes node which will be running 24/7 put me much over £100/month. I can't afford that... OK, so I will try to buy server on ebay. But what specification, price range? I have no idea about servers except that if I need VM I ask our Ops guy and he does his magic with our on permise server and after a while I have logging details to it. So I turn my eyes to internet and I found /r/homelab. And it turn that this is the best place to start with as there 100s of posts for and from people like me, who want to start their own homelab and need some guidance. After some research I found several interesting posts, especialy https://www.reddit.com/r/homelab/wiki/hardware and https://www.reddit.com/r/homelab/comments/5ldiel/so_you_wantgot_an_r710/ as I decided to go for Dell R710 server (or something similar).

After few weeks of looking for a deal with some frustrating results when sellers have finished auctions before end time and 
