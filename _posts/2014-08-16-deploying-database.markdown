---
layout: post
title:  "Deploying UDT/UDF/UDO on Production Databases"
date:   2014-08-16 20:00
categories: articles
---

You somewhere will find yourself creating User Defined Tables, User Defined Fields and User Defined Objects on SAP Business One. This is all easy done on SAP Business One client and you can easily work with it on addin development, the same way you did with B1Studio. The problem arises when you need to deploy it on a production database, were you'll need to recreate all your stuff. Dover deploy those objects on a production database, automatically creating and updating it when necessary.

### Exporting your data

Exporting your data is pretty simple. Just select what you want to export on Administration -> Add-Ons -> Export DB information, like below

![Export UDT Print]({{ site.url }}/images/deploying-database/export-table.png)

You can hold CTRL or SHIFT and select multiple lines

![Export UDF Multiple]({{ site.url }}/images/deploying-database/export-multiple.png)

### Importing your data

Import the exported files into your solution and mark then as Embedded Resource, in the Properties window

![Solution with embedded resources]({{ site.url }}/images/deploying-database/solution.png)

And annotate your code

{% highlight C# %}
[ResourceBOM("Transaction.UDF.xml", ResourceType.UserField)]
[ResourceBOM("Transaction.UDT.xml", ResourceType.UserTable)]
class Program
{% endhighlight %}

That's it! When annotating your code, the first parameter is the fully qualified name of your embedded resource, and the second parameter is the resource type. Right now Dover supports the following resource types:

* UserFields
* UserTables
* UDO

### Installing it

Go to an empty database, install Dover and deploy your addin. You'll notice that before saving the addin into the database Dover will ask you if you're sure you're going to do this, because this action will change the database structure.

![Database change warning]({{ site.url }}/images/deploying-database/dbwarn.png)

This is not a dumb installation procedure. Dover actually check for each missing UDT and UDF, and for each UDO that has changed. This is really important for support, because the support person upgrading an addin can know previously what will be done. If it's just an addin upgrade without database upgrade, the support person can upgrade an addin without interrupting the end user, otherwise he/she will have the opportunity to schedule the best time to do this with your customer.

After you confirm it,the first time the addin start up it will upgrade the database. If you want to force a database installation, you can select your addin and hit Install

![Database install]({{ site.url }}/images/deploying-database/install.png)

You can check that the installation is actually testing the database by removing and installing the addin again. The second time, since all fields/tables/UDOs exists, it won't warn you.

### Try it

The code you saw in this tutorial is part of the Transaction tutorial. You can download it on the [download]( {{ site.url }}/downloads ) page.

