---
layout: post
title:  "The beggining"
date:   2018-03-04 17:24:53 +0000
categories: homelab server kubernetes
tags:
 - homelab
 - server
 - kubernetes
 
---

# Why I need a homelab?

Hi. My name is Marcin and I'm a Software Developer. Welcome to my blog which will be a journal for my new project - a homelab.

Initially my homelab will be limited to only one server as I hope this will be enough for the project which aim is to set up a cluster of Linux virtual machines (VM), which I will use to learn (link)kubernetes. Currently at work I'm working on a project which is build around kubernetes (k8s) which is a new technology for our team. This gives me an oportunit to use this exciting technology in a real life project, but I am also limited by the scope of it (not surprise). Using my own cluster will alow me to "go wild" and explore parts of k8s which otherwise I won't have chance to use at work. Setting my own cluster let me also learn more about virtualisation. 

There are many other places over the internet which show how to set up a kubernetes cluster, why I then decide to create new blog about it? It will be journal for me to document my progress and back to it when I need it. I hope that it will also help other beginners in their own journey. There are few things which I think will be unique to my blog. One of them is that the server hosting the cluster must be configured by myself. No I won't be using AWS or any other cloud provider. I will start with a bare metal server. Another one is that I have no idea how to do it and I need to learn everything mostly from scratch. That would be interesting ride which keeps me excited.

{% include note.html content="
**Disclaimer:** I have little to none experience with servers and networking. There will be many trials and errors, but that's where all fun is - to create something and learn at the same time.
" type="danger" %}

# How all this have started?

The idea to buy a server has come from an [OpenJDK infrastructure](https://github.com/AdoptOpenJDK/openjdk-infrastructure) session at last [LJC Unconference](http://unconf.londonjavacommunity.co.uk/), when one of attendees has mentioned that he bought a cheap rack server on ebay and he has made others excited about the deal. At that time I've started to use (learn) kubernetes in my work, and it needs at lest 2 nodes to form a cluster. But how I can learn it at home? Quick calculation of cost to set up the kubernetes cluster in AWS which might be running 24/7 put me much over £100/month. I can't afford that... also experience from an university, where one my colleagues had to settle with Amazon payment for over £1k, because he has forgotten to switch off AWS instances at the end ot the session put me off from this idea. Other solution came from the above session "OK, lets try to buy an used server on ebay". 

![img]({{ '/assets/images/2018/03/LJC_unconference_2017.jpg' | relative_url }}){: .center-image }*LJC unconference where homelab idea has born.*

# Shopping time!!!
But where to start? What server spec I should look at? ? I have no idea about servers except that if I need a virtual machine at work I ask our ops guy and he does his magic on company servers and after a while I have logging details to the requested VM. I've turned my eyes to internet where I've found [/r/homelab](https://www.reddit.com/r/homelab/). And it turns that this is the best place to start as there are 100s of posts from people like me, who want to start their own homelab and need some guidance. After a bit of research I found several interesting posts, especialy [/r/homelab - Hardware Guide](https://www.reddit.com/r/homelab/wiki/hardware) and [/r/homelab - So you want/got an R710...](https://www.reddit.com/r/homelab/comments/5ldiel/so_you_wantgot_an_r710/) as I decided to buy Dell R710 server (or something similar).

After few weeks of looking for a deal with some frustrating results when sellers have finished auctions before end time, I have snapped great deal (in my opinion) on Dell PowerEdge R710 server.

![img]({{ '/assets/images/2018/03/ebay_won_auction_r710.jpg' | relative_url }}){: .center-image }*Great deal for R710 secured.*

Configuration:
* 2x Xeon E5504 (4 cores, 2GHz, 4 Mb cache) <span class="fa fa-thumbs-down text-danger"></span> <span class="fa fa-thumbs-down text-danger"></span>,
* 16x 4GB ECC DDR3 1066MHz Ram <span class="fa fa-thumbs-up text-info"></span> <span class="fa fa-thumbs-up text-info"></span> <span class="fa fa-thumbs-up text-info"></span> (but limited by the processor to 800MHz)<span class="fa fa-thumbs-down text-warning"></span>,
* 6x 3.5" HDD caddies + 2x 2.5" adapters<span class="fa fa-thumbs-up text-info"></span>,
* 8x 1Gb NICs (4 embedded and 2x 2 on PCI card) <span class="fa fa-thumbs-up text-info"></span>,
* RAID PERC6/i <span class="fa fa-thumbs-up text-info"></span> <span class="fa fa-thumbs-down text-warning"></span> (there are mixed opinioon about it but for my use case I think it is perfectly fine... will see),
* iDRAC Express and Enterprise <span class="fa fa-thumbs-up text-info"></span> <span class="fa fa-thumbs-up text-info"></span>.

There is no HDs but I've got some from my friend:
* 3 x 3.5" SATA 1TB
* 1x 2.5" SATA 512GB

Below are few pictures of it.

![img]({{ '/assets/images/2018/03/R710_front_pane_view.jpg' | relative_url }}){: .center-image }*R710 front view.*

![img]({{ '/assets/images/2018/03/R710_back_pane_view.jpg' | relative_url }}){: .center-image }*R710 back view.*

![img]({{ '/assets/images/2018/03/R710_without _top cover.jpg' | relative_url }}){: .center-image }*R710 without top cover (inside view).*

![img]({{ '/assets/images/2018/03/R710_the_heart_and_the_brain.jpg' | relative_url }}){: .center-image }*R710 the heart and the brain. Beauty of 2 processors and 18 memory slots filled up.*

# What next?
OK, I've bought the server, which was the easiest part. Now I need to run it. First boot and... there are completely different POST messages compared to what I know from desktop computers. I think I need to open google and /r/homelab again...
