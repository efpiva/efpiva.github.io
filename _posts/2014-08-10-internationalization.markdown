---
layout: post
title:  "Working with internationalization"
date:   2014-08-10 20:00
categories: articles
---

If you plan to deploy your solution on multiple countries, you should start writing your application with internationalization in mind. Dover integrates .NET i18n support with Business One nicely and gracefully. You can use it without any additional configuration, as it will be shown in this tutorial.

### Basic .NET i18n

On .NET basically you create a resource file for each idiom you're going to support and one resource without an idiom attached to it, that will be used by default if no idiom is encountered. To determine the idiom, at application startup .NET set all threads with the operational system language as the thread language, setting the CurrentUICulture thread information.


### What Dover do for you

* The CurrentUICulture of all threads are automatically configured with the language Business One is running. So instead of using default .Net CultureInfo, it already set up it for you depending on the language Business One is running.

* After reading all Form resources, it will parse all forms and if it finds any text that references a resource file, it will replace it.

* Menus can be created using their description from a resource file.

* LanguageChange event is handled by Dover, reconfiguring all threads and forms properly.

### How to identify a resource property

To work with i18n resources, all you need is to reference the fully qualified name of your resource property. So supose your application has the default namespace of FormI18N, and you create a resource file called Messages.resx on your project root structure. If you need to reference the property named FormTitle, just write FormI18N.Messages.FormTitle.

To localize your resources using ResourceName.code.resx. If you want to add a new resource file for idiom Portuguese, more specifically Brazilian Portuguese, add a new resource file called Messages.pt-BR.resx. If you're not sure what code you should use, check this [Table of Language Culture Names](http://msdn.microsoft.com/en-us/library/ee825488(v=cs.20).aspx).


### UserForms and SystemForms

Work with UserForms and SystemForms is quite simple. Just create a form as you would do, but instead of writing texts into description/caption/titles fields, write the fully qualified name of your resource property, like done below for this UserForm

![User Form with i18n tags]({{ site.url }}/images/i18nexample/userform.png)

Or here in this SystemForm

![User Form with i18n tags]({{ site.url }}/images/i18nexample/systemform.png)

And that's it! When you open your forms in SAP Business One, they'll appear with the corresponding idiom text

![User Form with i18n tags]({{ site.url }}/images/i18nexample/businessone.png)

For this example, this were the resources created

![User Form with i18n tags]({{ site.url }}/images/i18nexample/resources.png)

#### UDOForms

When working with UDO, things are not as nice as with UserForms and SystemForms. This is basically because when working with UDO, Business One stores a XML file on database, and some operations like renaming Grid , Matrix or radio buttons do not work properly using XML batch actions on UI API. But having a XML form in the database is nice because a lot of events are handled automatically by Business One, like links (yellow arrow). So how can we use this and mix with i18n?

The answer is: store an empty XML form in the database, and work the same way we're working with SystemForms, that is, updating the form created by the system.

So create your UDO the same way you would, and translate it using resources the same way we did with UserForms and SystemForms

![UDO form on B1Studio]({{ site.url }}/images/i18nexample/udo-form.png)

Export this UDO to a .srf file, and after you've done that, clear everything except O_U_E element (EditText) and 1 Button. This is part of the trick, the element 0_U_E is expected by business one while working with links, because this EditText is always bind to the DocEntry/Code field of the UDO. The Button 1 is the default OK/Search Button of forms. To prevent those elements from showing into the form and having a bad user experience (elements showing and "blinking" on the screen), move then outside the form boundaries, setting the left attribute to a big value, for example 1500. Your form should look like this

![Empty UDO]({{ site.url }}/images/i18nexample/empty-udo-form.png)

Now you can export your form to the database, right clicking on it.

Now, one last trick. By default, UDOs do not have a resource because they're read from the database. Import the .srf file we've exported previously and import into our solution. Do not forget to mark it as an embedded resource. Now specify this resource in the Form attribute you're implementing your UDO.

{% highlight C# %}
[FormAttribute("UDO_FT_TestUDO", "I18NExample.UDOForm.srf")]
public class UDOForm : DoverUDOFormBase
{% endhighlight %}

When you open your form without our solution running, it will look like this

![Empty Form on B1]({{ site.url }}/images/i18nexample/empty-form-on-b1.png)

But with the solution running, it open nice and it's still fast!

![Nice UDO on B1]({{ site.url }}/images/i18nexample/running-form-on-b1.png)

### Menus

Menu i18n is quite simple. On this I18N tutorial we have the following menus until now.

{% highlight C# %}
[Menu(String="DoverTutorial", Type=BoMenuType.mt_POPUP, UniqueID="doverTut", FatherUID="43520")]
[Menu(String="My Form", Type=BoMenuType.mt_STRING, UniqueID="doverTutForm", FatherUID="doverTut")]
[AddIn(Description="My Test App")]
class Program
{% endhighlight %}

To enable i18n, just change String property to i18n property, like this:

{% highlight C# %}
[Menu(i18n="I18NExample.Messages.MenuDoverTutorial", Type=BoMenuType.mt_POPUP, UniqueID="doverTut", FatherUID="43520")]
[Menu(i18n="I18NExample.Messages.MenuForm1", Type=BoMenuType.mt_STRING, UniqueID="doverTutForm", FatherUID="doverTut")]
[AddIn(Description="My Test App")]
class Program
{% endhighlight %}

### Deploying an i18n addin

To deploy an i18n addin is easy. You'll notice that on your output folder you'll have your application, in our case I18NExample.exe, and a folder for each idiom you've configured.

Just pack your application and all corresponding idiom folders in a single zip file and deploy it.

So in your case we'll have a zip file that has I18NExample.exe and pt-BR/I18NExample.resources.dll.

### Test it

Change Business One idiom and notice everything work's smooth! Nice, isn't it?

If you want you can download the full example from our [download]({{ site.url }}/downloads/) page.
