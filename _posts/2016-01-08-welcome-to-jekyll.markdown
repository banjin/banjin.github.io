---
title:  "在github上搭建博客"
date:   2016-08-23 15:04:23
categories: [jekyll]
tags: [jekyll]
---

本教程是一个简易快速搭建 github博客教程，不涉及到具体的语法，只是为了搭建而搭建，如果想学习具体搭建细节，请参考其它文章。

简单说，只需要三步，就可以在 Github 搭建起一个博客：
	
	1.在 Github 上建一个名为 xxx.github.io 的库；
	2 把看中了的 Jekyll 模板 clone 到本地；
	3 把这个模板 push 到自己的库；
	
	具体：
		一，有自己的github帐号
		二，建立一个项目 username.github.io  username是自己的Github帐号用户名
		在本地 git clone  https://github.com/username/username.github.io.git
		
		在本地会产生一个username.github.io的目录，里面有一些Github默认生成的文件， 可以忽略（删除）
		在http://jekyllthemes.org/ 找jekyll模版，点击下载，然后在本地进行解压，将解压目录下的所有文件都拷贝到username.github.io目录下，修改_config.yml文件，将url换成自己的url 设置成http://username.github.io
		baser:’/‘,再将其它有关用户的信息换成自己的。
		然后 git add .    git commit -m “”  git push
		在浏览器中打开 username.github.io 即可，
		
在写新的博客时候，只需要在_post目录下创建一个md格式文件即可
文件格式必须和模版带的的第一个文件格式一致
Markdown 文件的命名类型为「日期(20xx-xx-xx) + 主题(英文) + 格式（md/HTML/markdown）」
	
that is all ,so easy,bye



[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
