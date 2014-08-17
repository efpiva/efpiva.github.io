---
layout: post
title:  "Automatic transaction support"
date:   2014-08-16 20:00
categories: articles
---

Transaction is something really important to guarantee your product is robust, but somehow negligenciated by developers, because it's a tedious and repetitive job. Dover automatically threats transactions for you, so even if you're a lazy developer like me, it will do the job! ;)

### The Use Case

We're going to create a fictitious application that create a UserTable called DOVER_TUT_AVGSALES, with two UserFields, one called AVG_SALES, that will have the average sales amount, and another called CardCode, that will handle the corresponding CardCode. The scope of this tutorial is not to teach you know to change database structures, if you need more information check it at [database deployment article]({{ site.url }}/articles/2014/08/16/deploying-database ). I'm also going to use Dover BusinessOneDAO to make our life easier, you can read more about it at [business one dao article]({{ site.url }}/articles/2014/08/16/dao).

Make sure all Business Partners have at least one sales, so the example will first work. Then create a new Business Partner in your database, so we're sure that some BP will fail, because a divide by zero exception will occur.

You can check that when the error occur, no value was updated because an automatic rollback was triggered.

### Business One Transaction

If we were not working with Dover, our method should be surrounded by a code that looks like that

{% highlight C# %}
bool nestedTransaction = false;
if (company.InTransaction)
    nestedTransaction = true;
else
    company.StartTransaction();

try
{
    // DO SOME WORK HERE
    if (!nestedTransaction)
        company.EndTransaction(BoWfTransOpt.wf_Commit);
}
catch (Exception e)
{
    if (company.InTransaction) // prevent the case any nested transaction that did rollback
        company.EndTransaction(BoWfTransOpt.wf_RollBack);
    throw e;
}
{% endhighlight %}

So first we need to check if we're in a nested transaction and only commit if not. The same check we need to do at rollback, because if we try to rollback twice we will face another error.

So it's a nice code to copy and paste all arround your project, and error prone. You may forget to do this or even worse, implement wrongly. If that's the case you'll need to check all your code again and fix it multiple times. It doesn't sound smart, rigth?

### Proxies

To make your life simplier, Dover can proxy your implementation and do it automatically to you. Proxies are a form to implement AOP (Aspect Oriented Programming) and widely used on Dover Framework. 

If this is done automatically, what do you need to worry about? Almost nothing, just be sure to follow this two simple rules:

* Your class **must** me public. If you want to hide some implementation, no problem, make all methods internal or private. But to be able to proxy it, Dover must be able to override it, so your form classes must be public.
* Dover Framework should be able to override your event implementation. That means that your methods that will be proxies should be virtual and protected, never private. Following there is an example.
 
{% highlight C# %}
protected virtual void myMethod()
{% endhighlight  %}

For proxies Dover uses the library DynamicProxy from the Castle Project. You can get more information on their [site]( http://www.castleproject.org/projects/dynamicproxy/ ), if you're interested. There is also a nice [tutorial]( http://kozmic.net/dynamic-proxy-tutorial/ ) on DynamicProxy.

### Using Dover Transaction Proxy

To use it it's quite simple. Just annotate the class and method you're going to need transaction with a [Transaction] tag, like bellow.


{% highlight C# %}
[Transaction]
protected internal virtual void UpdateBusinessPartners()
{
    List<string> cardCodes = b1DAO.ExecuteSqlForList<string>(this.GetSQL("ListBP.sql"));
    foreach (string cardCode in cardCodes)
    {
        BPSales sales = b1DAO.ExecuteSqlForObject<BPSales>(string.Format(
            this.GetSQL("GetSalesValue.sql"), cardCode));
        string code = b1DAO.ExecuteSqlForObject<string>(string.Format(
            this.GetSQL("GetAVGSalesCode.sql"), cardCode));
        if (code == null)
        {
            code = b1DAO.GetNextCode("DOVER_TUT_AVGSALES");
            b1DAO.ExecuteStatement(string.Format(this.GetSQL("InsertAVGSales.sql"),
                code, cardCode,
                (sales.DocTotal/sales.Quantity).ToString("0.000000", CultureInfo.InvariantCulture)));
        }
        else
        {
            b1DAO.ExecuteStatement(string.Format(this.GetSQL("UpdateAVGSales.sql"),
                code, (sales.DocTotal/sales.Quantity).ToString("0.000000", CultureInfo.InvariantCulture)));
        }
    }
}
{% endhighlight %}

Since Dover Transaction class is derived from the Windsor Castle project, you`ll need to reference Castle.Windsor.dll in your project. You can download it [here](https://github.com/efpiva/dover/tree/master/Assemblies).

The full example you can download at the [download]({{ site.url }}/downloads/) page.
