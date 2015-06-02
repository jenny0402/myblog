---
layout: post
title: Java ClassLoader的一点心得
date: 2012-04-03 02:03:37
---



最近在做Java
ClassLoader的东西，看了一些书，也上网查了一些资料。网上讲得很多，但能够系统回答我的问题的并不多。我总是找一点，做一点，一路走下来，走了不少弯路，也学到了很多。这里做一个总结，对网上哪些无私帮助别人的大牛们也是一点回馈，对自己的未来的学习也是一个笔记：

1，是loadClass还是findClass？
在Java1.2之前，只有loadClass
method可以overwrite。之后有了findClass，因为双亲委派的机制，为了防止错失系统装载类的安全机制，总是先委托父母类去装载类，无法装载的时候在启动自己的findClass method去装载自己所需的类。但这样一来，很多时候父类装载器完全能找到该类，那么你自己的一些需求就无法实现，比如说在装载类之前先做一个security
check或解密。有时候系统的security
manager无法实现你的security implementation，你就需要overwrite
loadClass,在委托父类装载器装载之前先加入一些自己的东西，或者完全用自己的装载器装载类。这个method可以是final，这样别人就无法overwrite你，不能绕过你的安全机制。

2，Get Jar file Location.

class.getProtectionDomain().getCodeSource().getLocation().getFile();
在这里不得不提一个叫iteye的网站，里面有很多好文章，给予了我很多帮助。

3，set your ClassLoader as the current ClassLoader
CustomerClassLoader loader = New CustomerClassLoader();
Thread.currentThread().setContextClassLoader(loader);

但是我在实际应有中发觉，当你新建一个object的时候，还是会使用到系统默认的AppClassLoader，我无法解释为什么。问到供应商时，他们说你只能完全抛弃他们的ClassLoaer，新建一套自己的ClassLoader系统。


4， java security
plicy and security manager
这一部分我看得不多，但是通过java.policy文件你可以规定什么代码才可以新建一个ClassLoader或者设置为contextClassLoader。这个权限可以通过SignedBy
或者CodeBase来规定。JBoss policy file是个很好的例子。据说你还可以实现自己的security
manager, 完成更强大的功能。这部分我没有细看。

5，-Xbootclasspath
这是Java Application
Launch一个及其强大的功能。你可以写一个自己的Integer类，命名成java.lang包，编译成jar文件，放在-Xbootclasspath里面，就可以取代JVM系统自带的Integer类，想干吗就干吗，多好啊。

写得很乱，全当是给自己工作的一份总结，以便未来查询。


