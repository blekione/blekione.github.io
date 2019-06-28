---
layout: post
title: How to enable HTTPS in Thorntail

date:   2019-06-27 20:00
description: A guid how to enable HTTPS protocol in a Thorntail service.

comments: false

tags:
- java
- development
- microservices
---

Communication in the out-of-the-box Thorntail service is carried over HTTP. However, [the world tries to move to HTTPS](https://developers.google.com/web/fundamentals/security/encrypt-in-transit/why-https) to embrace security, which means sometimes, even for a development, HTTP is not enough. For example, [Swagger UI](https://swagger.io/tools/swagger-ui/), one of the Thorntail’s [fractions](https://docs.thorntail.io/2.4.0.Final/#fractions_thorntail), for "Try it out" creates API endpoints URLs with HTTPS protocol, even if the service is served over HTTP.

![img]({{ '/assets/images/2019/06/swagger-UI-https-link-example.png' | relative_url }}){: .center-image }*Example of HTTPS link created for HTTP service by Swagger UI*

But the main reason for enabling HTTPS should be the security of service users. By no means, I am a security expert, but setting up HTTPS should be a minimum requirement from each web service.

# Enabling HTTPS in Thorntail

## Reverse proxy HTTPS traffic to HTTP service.

In production, I would recommend using [Cetrbot](https://certbot.eff.org/) to automate certificate issuance from [letsencrypt.org](https://letsencrypt.org/) free Certificate Authority (CA), and a proxy server (for example Nginx) to [reverse proxy HTTPS traffic into HTTP service](https://www.digitalocean.com/community/tutorials/understanding-nginx-http-proxying-load-balancing-buffering-and-caching).

## Configure Thorntail to use Java Keystore

I recommend the above solution as it allows to automate the process of renewing SSL certificates. However, if you are using CA other than Let’s Encrypt, or you already have a certificate, then Thorntail can be configured to use [Java Keystore](https://en.wikipedia.org/wiki/Java_KeyStore). These [stackoverflow question](https://stackoverflow.com/a/8224863/1779488) and [blog post from Oracle](https://blogs.oracle.com/jtc/installing-trusted-certificates-into-a-java-keystore) give instructions on how to generate a keystore from an existing certificate.

Having keystore, we need to configure a Thorntail service to use it. It can be done in 2 simple steps described below.

### 1. Add keystore configuration to a service configuration file.

To configure a Thorntail service to use a keystore a following configuration needs to be added to [project-defaults.yml file](https://docs.thorntail.io/2.4.0.Final/#configuring-a-thorntail-application-using-yaml-files_thorntail)

{% highlight yaml %}

thorntail:

  https:

    keystore:

      path: <path_to_keystore_file>

      password: <password>

{% endhighlight %}

By default the service is configured to listen for HTTPS traffic on a port 8443. This can be changed by adding `thorntail.https.port` config:

{% highlight yaml %}

thorntail:

  https:

    keystore:

      path: <path_to_keystore_file>

      password: <password>

    port: <https_port>

{% endhighlight %}

### 2. Add management fraction to the service.

HTTPS listener is only enabled after management fraction is added to the project (and I’ve learned it the hard way :)). In the Maven project, fractions are added as project dependencies in `pom.xml` file:

{% highlight xml %}

<dependency>

 <groupId>io.thorntail</groupId>

 <artifactId>management</artifactId>

</dependency>

{% endhighlight %}

The version of the dependency is configured in Thorntail-bom, which should be added in `dependencyManagement` section of `pom` file, if project has been created with [Thorntail Project Generator](https://thorntail.io/generator/).

## Auto generate a self-signed certificate

Above 2 solutions might be an overkill for a local development though. In that case, Thorntail can be configured to auto-generate a self-signed certificate

{% highlight yaml %}

thorntail:

  https:

    certificate:

      generate: true

  port: <https_port>

{% endhighlight %}

Just to remember that the Undertow fraction must be added to the project (same as in the keystore solution above).
