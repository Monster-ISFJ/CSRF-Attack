# CSRF-Attack

## 配置

软件：Vmware Workstation 12 Player

系统：SEEDUbuntu12.04

网络：NAT

其他：

1. Firefox浏览器(安装好LiveHTTPHeaders插件，用于检查HTTP请求和响应)；
2. Apache网络服务器；
3. Elgg网页应用

### Starting the Apache Server

开启Apache服务器很简单，因为已经预装在SEEDUbuntu中了，只需要使用一下命令开启就行：

![配置](https://raw.githubusercontent.com/familyld/CSRF-Attack/master/graph/image2.png)

### The Elgg Web Application

这是一个开源基于网页的社交应用，同样已经预装在SEEDUbuntu里，并且创建了一些用户，信息如下：

![配置](https://raw.githubusercontent.com/familyld/CSRF-Attack/master/graph/image3.png)

###配置DNS

![配置](https://raw.githubusercontent.com/familyld/CSRF-Attack/master/graph/image4.png)

这个项目需要用到以上两个URL，启动Apache之后就可以在虚拟机内部访问到了，因为在hosts文件中已经配置了这两个URL是指向localhost的：

![配置](https://raw.githubusercontent.com/familyld/CSRF-Attack/master/graph/image5.png)

###配置Apache服务器

项目中使用Apache服务器作为网站的host，借助name-based virtual hosting技术，可以在同一台主机上host多个网站。配置文件default在/etc/apache2/sites-available/路径下：

![配置](https://raw.githubusercontent.com/familyld/CSRF-Attack/master/graph/image6.png)

可以看到Apache用的是80端口，每个网站都有一个VirtualHost块，指定网站的URL和对应的资源目录。比如上面这个block，就绑定了[www.CSRFLabElgg.com](www.CSRFLabElgg.com)和/var/www/CSRF/elgg路径，修改该路径下的文件就能改变网站的配置。

## 什么是CSRF攻击

CSRF全称Cross-Site Request Forgery，是跨站请求伪造攻击的意思，这里面有两个关键词，一个是跨站，一个是伪造。前者说明CSRF攻击发生时所伴随的请求的来源，后者说明了该请求的产生方式。所谓伪造即该请求并不是用户本身的意愿，而是由攻击者构造，由受害者被动发出的。流程如下：

![CSRF](https://raw.githubusercontent.com/familyld/CSRF-Attack/master/graph/image7.png)

用户首先登录了一个普通网站A，这时新的session就会被创建，用户的浏览器会自动存储cookie信息以及session id，这样短期内再打开网站或者网站的其他链接，用户就不需要重复登陆了。如果此时，用户访问了攻击者制作的恶意网站B，攻击者设置的对A的跨站请求就会被触发，并且由于浏览器的机制，cookie信息和session id会被附加到这个请求中(即使它是跨站的)。于是，A就会以为这个请求是用户发出的从而执行请求内容。只要稍加设计，攻击者就能利用该漏洞进行盗取用户隐私信息，篡改用户数据等行为。

具体来说，恶意网站可以伪造HTTP的GET和POST请求，在HTML的img,iframe,frame等标签中可以发起GET请求，而form标签可以发起POST请求。前者相对简单，后者需要用到JavaScript的技术。因为Elgg只使用POST，所以实验中会涉及到HTTP POST请求和JavaScript的使用。
