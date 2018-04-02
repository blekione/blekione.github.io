---
layout: post
title: A kubernetes cluster with minikube.
date:   2018-03-21 21:14
description: How to create a kubernetes cluster with help of minikube.
comments: true
tags: kubernetes

---

[Kubernetes (k8s)](https://kubernetes.io) has a quite steep learning curve. It has many concepts which are new for someone who has no previous experience with containerisation and containers orchestration systems before. Setting up a cluster manually on a bare metal could be also a tough task for a beginner, but if someone want to try there is a great guide how to do it written by [Kelsey Hightower](https://twitter.com/kelseyhightower) ["Kubernetes The Hard Way"](https://github.com/kelseyhightower/kubernetes-the-hard-way).

If you are not that brave, I recommend to start your journey with kubernetes from [minikube](https://github.com/kubernetes/minikube). Minikube automatically creates a kubernetes cluster with a single node on a local machine, which takes out a pain of configuring it by yourself. The guide [how to use minikube](https://kubernetes.io/docs/getting-started-guides/minikube/) on a kubernetes website is good and I've been using it myself. However for me some things are not exactly clear and this post is a supplement to this guide. It describes exact steps I've taken to run the kubernetes cluster with minikube.

For me, the biggest advantage of minikube is that there is no requirements for an additional configuration of the cluster, you just start minikube and it's done. The only extra thing that is required is a hypervisor, as minikube creates a virtual machine which will be used as a cluster node. A list of supported hypervisors can be found at [install minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) page. By default minikube uses VirtualBox which is fine for me as this is the hypervisor I’m most familiar with and I have it already installed on my machine.

{% include note.html type="info" content="
Before I go any further I need to mention versions of the software I use in this guide:
* Linux Ubuntu 16.04
* Minikube version 0.25.0
* kubectl 1.9.3
With rapid development of kubernetes, it might be that in a few months this guide will be outdated.
" %}

# Installation.

At the start we need to install minikube. The below command downloads the latest version of minikube and installs it into the /usr/local/bin/ folder, which let us use minikube direct from the command line:
{% highlight bash %}
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latestwh/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
{% endhighlight %}

Minikube is used to bootstrap a kubernetes cluster and we need an another tool for communication with the cluster. This guide uses [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/), which allows managing of a kubernetes cluster from a command line. We use it to deploy, expose or configure behaviour of our applications in the cluster. kubectl can be installed the same way as minikube:
{% highlight bash %}
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/darwin/amd64/kubectl
&& chmod +x ./kubectl && sudo mv ./kubectl /usr/local/bin/kubectl
{% endhighlight %}

And that is all we needed at the beginning. Let’s check if everything is working by creating a kubernetes cluster. To start minikube:
{% highlight bash %}
minikube start
{% endhighlight %}
![img]({{ 'assets/images/2018/03/minikube_start.png' | relative_url }}){: .center-image }

To stop minikube (but we won't do it now):
{% highlight bash %}
minikube stop
{% endhighlight %}

During the startup, minikube configures kubectl to communicate with the cluster by setting [a context](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/). To check if a cluster initialisation has been successfully:
{% highlight bash %}
minikube status
{% endhighlight %}
![img]({{ 'assets/images/2018/03/minikube_status.png' | relative_url }}){: .center-image }

, and
{% highlight bash %}
kubectl cluster-info
{% endhighlight %}
![img]({{ 'assets/images/2018/03/kubectl_cluster_info.png' | relative_url }}){: .center-image }

, and
{% highlight bash %}
kubectl get nodes
{% endhighlight %}
![img]({{ 'assets/images/2018/03/kubectl_get_nodes.png' | relative_url }}){: .center-image }

The last command returns available nodes. We see only one node, because minikube creates only one node, which is enough for the beginning.

# First deployment.

Initially the cluster is empty and we need to deploy some software to it. The easiest way to do it is by using a _kubectl run_ command:
{% highlight bash %}
kubectl run diffusion --image=pushtechnology/docker-diffusion:6.0.2 --port=8080
{% endhighlight %}

Where:
* _diffusion_ - A name of the Deployment created by the run command. _kubectl run_ also creates a label run=diffusion. [Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) are a very important feature of kubernetes as they allow for grouping cluster objects into logical groups. For example _kubectl run_ creates same label not only for the deployment but also for all pods created by this deployment, making a logical set on which other operations can be performed.
* _--image=pushtechnology/docker-diffusion:6.0.2_ - A hub.docker.com image in form of username/repository:tag which will be pulled to a docker container. The tag can be omitted, in which case the latest image version will be downloaded.
* _--port=8080_ - An internal port on which the container will be exposed.

The above command creates [a Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) object which instruct kubernetes about a desired state of [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/). Pods are the smallest deployable units in kubernetes. Pods contain a docker container(s) with your application and are deployed within available [Nodes](https://kubernetes.io/docs/concepts/architecture/nodes/).

{% include note.html type="info" content="
In this guide I use [Diffusion server](https://www.pushtechnology.com/developers/) docker image, the product I work on in Push Technology. It is used here not only because I'm familiar with it, but also because Diffusion server ideally fits into containerization pattern as it is stateless (if used without a persistence). It also offers a quick feedback if deployment was successful by accessing the server home page or console.
" %}

We can check the status of our Pods with
{% highlight bash %}
kubectl get pods
{% endhighlight %}
![img]({{ 'assets/images/2018/03/kubectl_get_pods.png' | relative_url }}){: .center-image }

We can see that our application has been deployed successfully (Ready: 1/1, Status: Running), but it is not visible/accessible from outside of its Pod. To expose it to the outside world:
{% highlight bash %}
kubectl expose deployment diffusion --type=NodePort
{% endhighlight %}
This creates [a Service](https://kubernetes.io/docs/concepts/services-networking/service/) object for the Deployment with label "diffusion". Service is an abstraction to define a policy to access a group of pods from the specified deployment. Based on the availability policy, different types of services will be specified. The NodePort type means that the "diffusion" service will be available on a cluster IP address under a port assigned from the range of available ports. We can find this port by executing:
{% highlight bash %}
kubectl get services
{% endhighlight %}
![img]({{ 'assets/images/2018/03/kubectl_get_services.png' | relative_url }}){: .center-image }

and it shows all service objects created in the cluster. We are interested in a "diffusion" service, which is exposed on a port 30687 (a random port from a defined range).

Minikube provides a mean to check an url under which our application is running:

minicube service diffusion --url

If you have follow this guide, copying that url into web browser should display Diffusion home page.
![img]({{ 'assets/images/2018/03/minikube_service_diffusion_url.png' | relative_url }}){: .center-image }

# Useful tools.

And that's it. We just have deployed our first application into a kubernetes cluster. This is just beginning which let you start playing with kubernetes. Before I finish I want to briefly introduce couple tools, which I think, are useful when using kubernetes. The first one is kubernetes dashboard. The dashboard provides GUI interface with overview of cluster elements, their statuses, and statistics which are useful for a cluster administration. Minikube deploys the kubernetes dashboard by default. To use it type

{% highlight bash %}
minikube dashboard
{% endhighlight %}

and the dashboard should be opened in a new tab of the web browser.
![img]({{ 'assets/images/2018/03/minikube_dashboard.png' | relative_url }}){: .center-image }


Another useful tool is kubectl exec. It executes given command inside specified container. For example:

{% highlight bash %}
kubectl exec -it jenkins-586886c599-l7ldd sh (change to diffusion)
{% endhighlight %}
will run a bash terminal for the container inside the pod named jenkins-586886c599-l7ldd. An -it flag means that command will be running in interactive mode. This mode lets you actually interact with sh terminal. If executed command doesn't require interaction with user then the flag can be omitted.

I hope that above provide enough information to start with kubernetes. THis setup allows to learn what all that Deployments, Replica Sets, Pods, Services etc. mean and how to use them. However minikube creates a very limited environment. Only 1 node, deployed locally within virtual machine is not suitable for a production or for more advanced scenarios. For this we need different toolset which I will describe in future blog posts.

