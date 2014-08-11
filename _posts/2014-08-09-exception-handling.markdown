---
layout: post
title:  "Exception Handling support"
date:   2014-08-09 20:00
categories: articles
---

We test it, we try to break it, but no matter how hard you try, your solution may eventually crash on the customer environment. You must be prepared to handle that kind of situation, otherwise you'll spend a lot of time (and money) on support. This article show what Dover has to support you with that challenge.

### Events

On SAP 9.0, we have B1Studio and a nice new range of events. Before that almost 80% of the work was done on ItemEvent and exception handling was something easy. Just surround your ItemEvent with a big try { } catch { } block, with some nice logging, and you're done.

But how can we do this with all sort of events in all sort of places? Now we can add events to Forms and to virtually any Item. Of course we could add a try { } catch { } to all events, but this is tedious, and tedious works are error prone.

The solution used on Dover is to automatically proxy all your classes. Proxies are a form to implement AOP (Aspect Oriented Programming) and widely used on Dover Framework. They're used basically to wrap all events into an automatic try {} catch {} block, with nice logging and trace windows. Doing so we prevent any crash on our UI-API instance, since if there is any uncatch event on UI-API, it stop working. One good advantage of that approach is that there is more information for you to support your customers, because of the trace window and enhanced logging.

If this is done automatically, what do you need to worry about? Almost nothing, just be sure to follow this two rules:

* Your class **must** me public. If you want to hide some implementation, no problem, make all methods internal or private. But to be able to proxy it, Dover must be able to override it, so your form classes must be public.
* Dover Framework should be able to override your event implementation. That means that your event methods should be virtual and protected, never private. Following there is an example.
 
{% highlight C# %}
protected internal virtual void myButton_ClickAfter(object sboObject, SBOItemEventArg pVal)
{% endhighlight  %}

For proxies Dover uses the library DynamicProxy from the Castle Project. You can get more information on their [site]( http://www.castleproject.org/projects/dynamicproxy/ ), if you're interested. There is also a nice [tutorial]( http://kozmic.net/dynamic-proxy-tutorial/ ) on DynamicProxy.

### Exception Trace Window

What happens when an exception happens? A window like that opens:

![Exception trace window]( {{ site.url }}/images/exception/exception-trace-window.png )

Notice that you have the exception message, the exception StackTrace and, if there is any Inner Exception, you can easily access it. That's really nice for support! 

### Logging

All events are also logged for your convenience. For each running instance of Dover, you can access all it's information on %APPDATA%\Dover\SERVER-COMPANYDB\. You'll notice that you have some logging files on that folder, and also some config files. Basically you have a Dover.config, that tells how the core component logging will happen. For each application, you'll have an AppName.config also.

The configuration file is for the framework Log4Net. It's very flexible, you can choose multiple logging appenders, meaning you can log on multiple medias. If you check the config file, there is a custom loggin appender from Dover, that prints info messages on Business One, and is reponsible for all Exception Trace window stuff on Errors. 

You can check the full documentation for lof4net on [apache log4net website](http://logging.apache.org/log4net/)



