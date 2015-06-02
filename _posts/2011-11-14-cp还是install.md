---
layout: post
title: cp还是install
date: 2011-11-14 05:26:28
---



最近因为项目的原因在学习Makefile。在install的地方我copy了别人的mkdir和cp，觉得这样就够了。但是我们部门的大牛在帮我看程序的时候，觉得这样不够专业，就把它改成了linux的install命令，可惜不工作，他的命令如下：
install:$(program)
    install -D $(program)
$(program)-$(ver)/bin
    install -D lib1.so
lib2.a  
$(program)-$(ver)/usr/lib

可惜不工作，我测试了一下，发觉用D选项，目标目录后面要跟文件名，还有，一旦source超过一个，install就无法辨认。既然牛人喜欢install，我就上网查了一下，终于搞成功了，但很麻烦。首先是生成目录，再拷贝任意多个文件：

install:$(program)
    install
-d  
$(program)-$(ver)/bin/ 
  
    install -d  
$(program)-$(ver)/usr/lib
    install
-t   $(program)-$(ver)/bin/
$(program)
    install -t  
$(program)-$(ver)/usr/lib lib1.so lib2.a
 
成功是成功了，但这种方法看起来很锉，很不专业，于是在网上(Makefile Tutorial)又找到了一例子，似乎可以用，明天试试看，对牛人也是一个交代：
install: our_program
    install -m 0755 our_program
$(prefix)/bin
    install -m 0644 *.png
$(prefix)/share/our_program/icons

发现牛人学习的方式不是看书，而是边用变学，不懂就用help,man或者google，看来我得多学着点，将来才有找工作的资本。
－－－－－－－－－－－－－后续－－－－－－－－－－－－－－－－－－－－－
这个-m选项不会创建文件夹，所以-d选项还是必须的，然后再用-m选项拷贝文件。
至于install的优点，在网上查到一下内容
http://blog.yikuyiku.com/?p=2659
基本上，在Makefile里会用到install，其他地方会用cp命令。
它们完成同样的任务——拷贝文件，它们之间的区别主要如下：

1、最重要的一点，如果目标文件存在，cp会先清空文件后往里写入新文件，而install则会先删除掉原先的文件然后写入新文件。这是因为往正在
使用的文件中写入内容可能会导致一些问题，比如说写入正在执行的文件可能会失败，比如说往已经在持续写入的文件句柄中写入新文件会产生错误的文件。而使用
install先删除后写入（会生成新的文件句柄）的方式去安装就能避免这些问题了；
2、install命令会恰当地处理文件权限的问题。比如说，install
-c会把目标文件的权限设置为rwxr-xr-x；
3、install命令可以打印出更多更合适的debug信息，还会自动处理SElinux上下文的问题。

http://bbs.linuxpk.com/thread-32835-1-1.html
install 可以一句完成如下的命令
mkdir，cp，chown，chmod，strip


