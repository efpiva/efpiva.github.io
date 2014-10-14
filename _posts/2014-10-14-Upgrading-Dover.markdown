---
layout: post
title:  "Upgrading a release candidate"
date:   2014-10-14 10:00:00
categories: articles
---

If you're not using a release candidate, upgrading dover is quite simple. You normally won't even need to reinstall the Addon, just update the Framework.dll (or Framework.zip if you're using multiple resource languages) in module installation and update and you're done.

If you're using a release candidate, things are not that easy. Why? As the name says, it's not a final release, and some API may change, requiring full Dover upgrade.

For example, in 1.0.2 version all DLLs are compressed before being saved to the database, saving boot time. If you're using any version prior to that, data is not compressed and hence Dover won't understand it anymore on 1.0.2. Of course I could add some checking on the code to prevent that, but this would add unnecessary code and complex to the solution. When Dover becomes stable, no API changes would be allowed anymore until a major release is done.

### Reinstalling it

1. Remove Dover addon and remove all @DOVER\_ tables manually. Be aware that you'll need to reinstall all addins you had previously installed on Dover, so double check if you have them.
2. Remove %APPDATA%\Roaming\Dover folder. This is *not* strictly necessary since on boot it will be updated, but if you're facing problems, try to do that.
3. Install Dover and start it. All tables will be recreated and you're ready to reinstall your addins.

### When do you plan to release a stable release?

Right now I'm facing problems making it possible to work under SAP 9.0 AND 9.1, since the same COM elements are defined on different DLLs (SDK SAPbouiCOM dll on 9.0, SAPBusinessOneSDK dll on 9.1). I plan to make it possible to upgrade from 9.0 to 9.1 without the need to recompile and reinstall all addins, and also to make it possible to use addins compiled with 9.0 dll and 9.1 dll.

I have a possible solution for that, but again I might break the API, so I'm not confident enough to remove the Release Candidate flag yet.

