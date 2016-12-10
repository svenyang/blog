---
layout:        post
title:         "Ansible记录"
subtitle:      "备忘"
date:          2016-12-10
author:        "sven"
header-img:    "img/post-bg-2015.jpg"
tags:
    - 运维
---

### 引   
机器数量越多越多的时候，对于机器的管理就存在较大问题，尤其是慵懒的程序员来说，在缺乏运维的情况下，自己动手去搞一系列部署、发布修改配置有的时候就是噩梦，自己写expect有的时候考虑不到一些边界条件，或者临时想做什么事情，再去写脚本已经来不及，所以经常抓瞎  

楼主从腾讯离职后，以前使用的脚本扔到跳板机上了，没有保存在自己的电脑上，所以就全丢了。。。（傻逼了吧）不过也很久没用过了   

来了新公司，写一些代码部署，修改，等操作，机器量越来越多，伪创业公司缺乏统一完善的运维环境和人员，所以很多事情为了靠谱起见还是自己搞吧（被公司坑若干次）  

搜索到现在较多运维用的Ansible，觉得不错，符合以前的使用习惯，并且能很快上手，复杂简单的功能均可实现，并且用python写的，以后可以尝试自己加功能，so，昨晚简单的用了下，记录下Ansible基本规则，忘记的时候出来翻翻  

### 安装与依赖
> yum install ansible  
> brew install ansbile  
> apt-get install ansible  
> pip install ansible  

** 依赖于yaml配置，python库（用python写的）**   

### 原理
就是一个ssh，expect连上远程服务器后操作，因为是ssh，所以比较慢，还没深究是串行操作还是并行，如果服务器数量很多，几千台的规模，不建议用ssh方式管理主机。自己写agent，毕竟规模那么大了，怀念最初的TAF、以及SPP、SF2，虽然几年没用过了，毕竟框架算是先进生产力

### 连接
用ssh连接，自然涉及到登陆密码或者ssh rsa的免密登陆  
要是有密码，可以在hosts列表中写；如果没有密码，那简单了，hosts啥也不用配置，用户名正确就可以，然后在配置中关闭key提示选项`host_key_checking=false`

### 运行方式
描述的比较简单，因为昨晚半夜3点中看了一眼，先这么简单用吧  
ansible依赖**配置文件**和**主机列表**才能运行  

* 编译配置文件
 	- mac的目录为/usr/local/etc/ansible/，文件名为ansible.cfg，可以按照网上的说法仅填写几个选项，或者从安装包中复制  
楼主就写了三个选项  

	```   
	[defaults]  
	remote_post=22  
	private_key_file=~/.ssh/id_rsa  
	host_key_checking=false  
	```  

* 编译主机列表  
	- 主机列表和配置文件同样目录，文件名为hosts，或者自己随便写（运行的时候需要-i指定目录），写法如下 
	
	```
	#分类名，运行ansible写分类名就意味着对别名下面的列表都进行操作，写url应该也可以，不过没用过，不乱说
	[name]
	#最简单的写法，ip+ssh用户名
	127.0.0.1 ansible_ssh_user=***  
	```  
	
	> 如果觉得不过瘾，想写多一些参数，看[http://docs.ansible.com/ansible/intro_inventory.html](http://docs.ansible.com/ansible/intro_inventory.html)
	

### 运行
基本的命令   

```
ansible name -m shell -a 'ls /var/log/'  
ansible name -m command -a 'ls'  
ansible name -m script -a '~/run.sh'
ansible name -m copy -a 'src=~/aaa.txt dst=~/aaa.txt mode=644 owner=root'  
```  
* name就是前面那个分类名
* -m后面跟着方法，可以有shell或者command，或者script  
	- shell好用，因为支持管道，所以基本的命令不会出错
	- command，简单的单个命令还行
	- script，复杂的还是写个shell吧，看ansible说复杂的可以用ansible的playbook语法写，但是没必要再去熟悉一个脚本语言吧，如果shell可以简单实现的话
	- copy，复制文件专用
* -a 后面跟着命令或者是脚本的路径（-m script)  
	**部分情况下，命令需要使用shell执行，因为command不能用管道，所以会出错**
	
差不多就这些了，反正网上文档一大堆，google一下或者直接去ansible的doc文档看，更直接  

over
