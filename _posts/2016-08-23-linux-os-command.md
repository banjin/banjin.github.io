---
layout: post
title:  "linux 系统查看命令"
date:   2016-08-23 15:04:23
categories: [jekyll]
tags: [python]
---

本博客主要记录一些有关操作系统的命令，以便能够及时查看（更新中...）

### 操作系统命令
	uname -a #查看内核/操作系统/CPU信息
     Linux vagrant-ubuntu-trusty-64 3.13.0-68-generic #111-Ubuntu SMP Fri Nov 6 18:17:06 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux

	head -n 1 /etc/issue #查看操作系统版本
	     Ubuntu 14.04.4 LTS \n \l
	
	cat /proc/cpuinfo #查看CPU信息
	hostname #查看计算机名
	     vagrant-ubuntu-trusty-64
	
	lspci -tv #列出所有PCI设备
	lsusb -tv #列出所有USB设备
	lsmod #列出加载的内核模块
	
	env #查看环境变量
	
	
### 文件／IO 资源命令
	free -m #查看内存使用量和交换区使用量
	df -h #查看各分区使用情况
	du -sh <目录名> #查看指定目录的大小
	grep MemTotal /proc/meminfo #查看内存总量
	grep MemFree /proc/meminfo #查看空闲内存量
	uptime #查看系统运行时间、用户数、负载
	cat /proc/loadavg #查看系统负载
	mount | column -t #查看挂接的分区状态
	fdisk -l #查看所有分区
	swapon -s #查看所有交换分区
	hdparm -i /dev/hda #查看磁盘参数(仅适用于IDE设备)
	
	dmesg | grep IDE #查看启动时IDE设备检测状况

 ### 网络命令
 
 	ifconfig #查看所有网络接口的属性
	iptables -L #查看防火墙设置
	route -n #查看路由表
	netstat -lntp #查看所有监听端口
	netstat -antp #查看所有已经建立的连接
	netstat -s #查看网络统计信息
	
### 进程命令
	
	ps -elf #查看所有进程
	ps -aux #查看所有进程
	top #实时显示进程状态

### 用户命令
 	
 	w #查看活动用户
	id <用户名> #查看指定用户信息
	last #查看用户登录日志
	cut -d: -f1 /etc/passwd #查看系统所有用户
	cut -d: -f1 /etc/group #查看系统所有组
	crontab -l #查看当前用户的计划任务
	
###  服务命令

	chkconfig --list #列出所有系统服务
	chkconfig --list | grep on #列出所有启动的系统服务
	
### apt-get常用命令

	apt-cache search package 搜索包
	apt-cache show package 获取包的相关信息，如说明、大小、版本等
	sudo apt-get install package 安装包
	sudo apt-get install package - - reinstall 重新安装包
	sudo apt-get -f install 修复安装"-f = ——fix-missing"
	sudo apt-get remove package 删除包
	sudo apt-get remove package - - purge 删除包，包括删除配置文件等
	sudo apt-get update 更新源
	sudo apt-get upgrade 更新已安装的包
	sudo apt-get dist-upgrade 升级系统
	sudo apt-get dselect-upgrade 使用 dselect 升级
	apt-cache depends package 了解使用依赖
	apt-cache rdepends package 是查看该包被哪些包依赖
	sudo apt-get build-dep package 安装相关的编译环境
	apt-get source package 下载该包的源代码
	sudo apt-get clean && sudo apt-get autoclean 清理无用的包
	sudo apt-get check 检查是否有损坏的依赖


### root

	sudo password root  启用root权限，设置root 密码
	
	su root 切到root权限
	
	sudo password -l root 禁用root 帐号
	
### 修改主机名
	
	sudo gedit /etc/hostname
	sudo gedit /etc/hosts

[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
