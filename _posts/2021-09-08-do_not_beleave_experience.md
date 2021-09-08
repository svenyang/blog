---
layout:        post  
title:         "经验主义害死人"  
subtitle:      "一点点思考"  
date:          2021-09-08  
author:        "sven"  
header-img:    "img/post-bg-sea.jpg"  
tags:
    - 随笔
---

# 结论
因为文件命名错误，不符合jekyll的规范，导致github page一直没有更新成功

# 苦笑不得的过程
## 问题发现
当写了一篇文章push到github上后，发现迟迟没有更新，怀疑是不是文章的语法错误，导致的build 失败，但github只给我发了build CNAME的warning邮件，也没有说更新失败  

随后，开始了漫长的问题定位过程，在这个过程中，走了很多的弯路~~~~~

## 问题定位
1. 是不是没有push成功？  
上github看了源码，发现更新成功了

2. 是不是有语法错误？  
github未发送build error邮件，默认应该成功的

3. 是不是浏览器有缓存？  
清空了浏览器，用wget主页信息，发现也没有更新

4. 继续debug  
尝试本地编译，结果开启了繁琐的jekyll update过程
```
gem install jekyll bundler
cd blog
bundle exec jekyll serve
```
然后，发现，自己傻逼了，当初将.bundle的文件删除了，而我又不知道该文件应该重新创建~

5. 继续debug。  
既然无法这样build，那我这样吧
```
jekyll build --safe 
```
but，发现了若干warning以及error（都是无关大雅的本地编译问题），比如``Liquid Warning: Liquid syntax error (line 50): Unexpected character & in "site.duoshuo_share && site.duoshuo_username" in /Users/xxxxxx/Documents/blog/_layouts/post.html``

**解决之**

6. 继续debug。。  
偶尔发现，本地编译的_site没有生成那篇文章的结果，且没有error，那就奇怪了，为什么不参加编译呢，于是，用了下面的这个命令探查
```
jekyll build --safe -V
```
对，你没看错，加了-V，``-V, --verbose      Print verbose output.``
于是，看到了，没有显式的提示skip跳过新加的文件，而是，没有参与编译，没有任何的日志和新加的文件相关...

7. 继续debug。。。  
好了，定位到该文件有问题了，可能性有几个
- 文件名有问题
- 文件时间有问题
- 文件的编码有问题
so，经过排查，发现，文件命名出问题了，在日期后，不能直接加‘下划线_’，而需要加'中线-‘后才能加下划线  
哎。。。。。。。。。。。。。。。。。。。。。。。。
这个破问题搞了好久，还是自己对jekyll完全不了解，仅会简单的使用，而自己，最开始通过写过的文章，将这个显而易见的问题跳过了

# 教训
**不能想当然，不能经验主义**  
**不能想当然，不能经验主义**  
**不能想当然，不能经验主义**  
重要的事情说三遍