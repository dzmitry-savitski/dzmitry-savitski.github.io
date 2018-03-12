---
layout: post
title: "Java socket web shell from Controller or Servlet"
date: 2018-03-04 14:06:06
tags: java webshell
description: Turn Java Servlet or Spring MVC controller into interactive web shell
comments: true
---

## Idea

Hi! Is it possible to run an interactive shell session using only java servlets or Spring MVC controllers? The idea was to write someting like [php-findsock-shell](http://pentestmonkey.net/tools/web-shells/php-findsock-shell) using Java.  

## Implementation
Implementation for spring MVC controller can be found in my gist repo [here](https://gist.github.com/dzmitry-savitski/89540f13316e594b61420e07932053ae)

## Usage
Add the controller to your application and connect using netcat:
{% highlight bash %}
nc localhost 8080
POST /shell HTTP/1.1
Host: localhost
Content-Length: 2147483647
Accept-Encoding: identity
Transfer-Encoding: identity

{% endhighlight bash %}
![img]({{ '/assets/images/java-interactive-shell.gif' | relative_url }}){: .center-image }

## Details
It's possible to interact with **HttpServletRequest** using **javax.servlet.AsyncContext**:
>Java
{:.filename}
{% highlight java %}
final ServletOutputStream socketOut = asyncContext.getResponse().getOutputStream();
final ServletInputStream socketIn = asyncContext.getRequest().getInputStream();
{% endhighlight %}
Imagine, that we are sending very long **POST** request and server can interact with each sent line in runtime.

## Problem 1 - chunked encoding
By default servlet will send chunked response and the corresponding header for async streams:
**Transfer-Encoding: chunked**
This'll cause a mess in our interactive shell. To get rid of this - let's explicitly set content length for our response:
>Java
{:.filename}
{% highlight java %}
response.setContentLength(Integer.MAX_VALUE);
{% endhighlight %}

## Problem 2 - connection timeout
By default Tomcat sets 20 seconds as a connection timeout for our servlets (connectionTimeout in server.xml), so if we won't send any commands more than 20 seconds - our web shell will die. I didn't find any suitable way to bypass this limitation from java code for the current servlet (or controller). If you know such a hint - welcome to comments. 
