---
layout: post
title: 基于VirMach服务器的SSR搭建
categories: SSR
description: SSR搭建
keywords: SSR搭建,VPS,VirMach服务器
---

### 写在前面

最近一直没有科学上网，今天用的时候才发现之前用GitHub学生包白嫖的DigitalOcean的VPS已经过期了，怪不得没法科学上网了，咨询诸多大佬之后决定跟风超超，花了￥75买了个VirMach的最低配置VPS：
> Memory: 256 MB  
> CPU: 1 vCORE  
> Disk Space: 10 GB  
> Bandwidth: 500 GB  

- VirMach的VPS优势：首先当然是便宜啊！(谁让我穷呢-.-)。其次，这个厂商对国人友好：它居然支持支付宝支付(那时候我还担心是不是需要找超超帮我付款)

- 当然了，价格低的代价首先肯定是没有什么客服的($10一小时的客服还是算了吧)，其次，不支持免费更换IP，丫的可能会分配一个被大陆屏蔽的IP给你，要换的话$3一次，要换一个保证不被大陆屏蔽的IP：$5！！这很过分了，不过还好分配给我的IP没有被屏蔽，只是延迟不低就是了。

- 最后，购买链接[VirMach](https://billing.virmach.com/cart.php)。当然了，看我的文章肯定有福利：选择付款的方式记得选择“$12.5 USD Annually”方式，这样可以优惠两个月(Order annually to get 2 months of free service ; only pay for 10 months!),可别选错了。接着，在提交订单之前，在Subtotal右边有行小小的黄字：“Promo?”，目前没有发现别的能用的优惠码，官方有个“SAVE10”的优惠码，可以省10%的钱。拿走拿走别客气~
- 好了废话说到这，咱们正式开始今天的主题：

# A、SSR 安装:

（温馨提示本教程在CentOS 6.5 x86_64 Minimal系统下使用总结，别的是否适用不保证）

### 0.更新系统

`yum update`

### 1.下载并执行 ShadowsocksR 单/多端口一键管理脚本

`wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssr.sh && chmod +x ssr.sh && bash ssr.sh`

提示：如果运行上面命令出现wget not found之类的提示，则表明系统没有预装wget，先运行以下命令完成wget的安装：`yum -y install wget`

### 2.运行脚本后，按照步骤依次设置端口、密码、加密方式等

>  ShadowsocksR 一键管理脚本 [v2.0.38]  
>  ---- Toyo | doub.io/ss-jc42 ----  
>  1 安装 ShadowsocksR  
>  2 更新 ShadowsocksR  
>  3 卸载 ShadowsocksR  
>  4 安装 libsodium(chacha20)  
>  —————————  
>  5 查看 账号信息  
>  6 显示 连接信息  
>  7 设置 用户配置  
>  8 手动 修改配置  
>  9 切换 端口模式  
>  —————————  
>  10 启动 ShadowsocksR  
>  11 停止 ShadowsocksR  
>  12 重启 ShadowsocksR  
>  13 查看 ShadowsocksR 日志  
>  —————————  
>  14 其他功能  
>  15 升级脚本  
>  请输入数字 [1-15]：  

提示：安装过程中会询问是否设置协议和混淆插件兼容原版，是指兼容 Shadowsocks，可以根据需求进行选择，原则上不推荐使用 Shadowsocks 客户端，故输入 n

### 3.等待安装完成后，出现如下界面。这些信息是你客户端连接时需要输入的配置，以后也可以执行bash ssr.sh键入 5 进行查看

> ===================================================  

> ShadowsocksR账号 配置信息：  
> I  P       : `服务器IP`  
> 端口       : 2333  
> 密码       : doub.io  
> 加密       : aes-128-ctr  
> 协议       : auth_sha1_v4_compatible  
> 混淆       : plain  
> 设备数限制 : 0(无限)  
> 单线程限速 : 0 KB/S  
> 端口总限速 : 0 KB/S  
> SSR   链接 : `生成的SSR链接`  
> SSR 二维码 : `生成的SSR二维码链接`  
> 提示:  
> 在浏览器中，打开二维码链接，就可以看到二维码图片。  
> 协议和混淆后面的[ _compatible ]，指的是 兼容原版协议/混淆。  

> ===================================================  

##### SSR安装到此结束。

# B、锐速安装：

### 0.更新工具

`yum -y install net-tools`

### 1.安装锐速环境

`rpm -ivh http://soft.91yun.org/ISO/Linux/CentOS/kernel/kernel-2.6.32-504.3.3.el6.x86_64.rpm --force`

### 2.重启系统

`reboot`

### 3.安装锐速

`wget https://shiyu.pro/serverspeeder.sh && bash serverspeeder.sh`

### 4.最后关闭防火墙，防止出现偶尔无法连接的问题：

`service iptables stop`
`chkconfig iptables off`


##### 至此，SSR和速锐均安装完毕
Enjoy it！

### 写在最后
- 就这点东西居然写了一个多小时，简直了！MD语法一点也不熟...
- 欢迎指出错误@WeChat/@QQ：2420365124