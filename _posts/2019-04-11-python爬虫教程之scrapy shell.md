---
layout:     post
title:     python爬虫教程
subtitle:   python爬虫教程之scrapy shell
date:       2019-04-11
author:     abhhay
header-img: img/_500144148.jpg
catalog: true
tags:
    - python
    - 爬虫
---


### Python 爬虫教程 scrapy shell

##### 1什么是python shell

​	当你做一个很大的**scrapy**项目的时候，可以直接快速的调试你的xpath re是否

是正确的  类似于postman 一个专门测试 API 的工具

##### 2.下载：

我们在anaconda中搜索scrapy  点击apply

![](img/scrapy01.png)

##### 3.启动命令(在安装好有scrapy的环境里面)

`scrapy shell  “website name”`

所有数据都会包装成一个resposne对象供给使用者使用

你可以想象成一个草稿纸版简易的scrapy项目

response.body

response.header

response.xpath()

response.css()

----

##### 4.测试小案例

我们用 [scrapy 爬虫实验室](http://lab.scrapyd.cn) 作为测试案例

我们在控制台输入 ：

`response.css('title')`

![](https://github.com/abbhay/abbhay.github.io/blob/master/img/scrapy02.png)

##### 5.疑问----那么我们如何获取里面的信息呢

![](https://github.com/abbhay/abbhay.github.io/blob/master/img/scrapy03.png)

![](https://github.com/abbhay/abbhay.github.io/blob/master/img/scrapy04.png)

##### 6.查看数据

![](https://github.com/abbhay/abbhay.github.io/blob/master/img/scrapy05.png)

当我们输入的时候  回车就自己运行了 那我们如何进行多行输入

当我们使用for循环或者其他函数的时候  可以多循环 但是回车没法执行

这个时候我们就用 Ctri +Enter j就可以了

![](https://github.com/abbhay/abbhay.github.io/blob/master/img/scrapy06.png)

#####  7.通过结构化提取 查看数据是否是正确的

那我们再通过结构化提取 看看我们的数据是否在里面

![](https://github.com/abbhay/abbhay.github.io/blob/master/img/scrapy07.png)

说明数据在里面 我们的xpath语法正确

##### 结果

![](https://github.com/abbhay/abbhay.github.io/blob/master/img/scrapy08.png)

### 结语

***多敲代码，多学习，这些就会了***
>>>>>>> scrapy shell
