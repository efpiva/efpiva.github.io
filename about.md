---
layout: page
title: About
permalink: /about/
---

Dover is a Framework that enables rapid addon development for SAP Business One, developed by Eduardo Piva and sponsored by [GWE Added](http://www.gweadded.com.br), a SAP Business One VAR (Valued Added Reseller) in Brazil.

### Features

* Addin lifecycle management
   * Automatic creation and update of UDO, UDT and UDF
   * Automatic updates without installing new addons
   * Create test databases in a simple and easy way
   * Nice and usefull Warnings when an update will change database structure, easying support.
* Business One standard DAO
* Annotatted Transactions
* i18n Support for Forms
   * UDO
   * SystemForms
   * UserForms
* Enterprise logging using Log4Net
* Built in IoC container on Business One Studio that easies testing and maintenance
* Automatic proxy on BusinessOne events, preventing crashes and handling logging on a nice way, easying support.

### Why?

At GWE Added we faced some limitations while trying to use SAP Business One Studio, provided on SAP 9.0. It's not possible to define where forms are going to be loaded from, and neither manipulate it before rendering it on screen. So it was not possible to create solutions with multiple assemblies (an AddOn with multiples DLLs, for example), neither work properly with internationalization.

Those two features were blocking us from using Business One Studio, but we really enjoyed the benefits of the new event handling model. We already had some infrastructure for addon development with it's own limitation. Adapting it to work with Business One Studio would require a lot of work, and would not lead to a nice solution. That was when I decided to rewrite all our addon infrastructure development from scratch.

### License

The framework is licensed under GPLv3. So you can download the source code, compile it, modify it, as long as you respect all the license terms specified on the license.

This does not restrict the license of the solutions (called addins) you write on top of Dover. Your addins can have difference license models, being it open source or proprietary. 

If you have any questions, contact me.
