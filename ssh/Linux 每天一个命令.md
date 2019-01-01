# Linux 每天一个命令

## SSH

### SSH 介绍 
简单说，SSH是一种网络协议，用于计算机之间的加密登录。如果一个用户从本地计算机，使用SSH协议登录另一台远程计算机，我们就可以认为，这种登录是安全的，即使被中途截获，密码也不会泄露。最早的时候，互联网通信都是明文通信，一旦被截获，内容就暴露无疑。1995年，芬兰学者Tatu Ylonen设计了SSH协议，将登录信息全部加密，成为互联网安全的一个基本解决方案，迅速在全世界获得推广，目前已经成为Linux系统的标准配置。

### SSH 基本命令

1. SSH 远程登录
```ssh user@host```
![Alt text](https://github.com/badcyc/dailyLinux/raw/master/ssh/ssh_1.png)

2. SSH 的默认端口是22。使用```-p```参数可以修改
下面通过```ssh -p 22```登录
![Alt text](https://github.com/badcyc/dailyLinux/raw/master/ssh/ssh_2.png)

### 公钥登录

1. 先生成一对密钥文件（公钥和私钥）

![Alt text](https://github.com/badcyc/dailyLinux/raw/master/ssh/ssh_3.png)
以上命令等价于 ssh-keygen -t rsa
-t:指定密钥的类型,默认为SSH-2 的rsa类型;
运行上面的命令后，系统会出现一系列提示，可以一路回车。特别说明，其中有一个问题是，要不要对私钥设置口令（passphrase），如果担心私钥的安全，可以设置一个。运行结束以后，会在 ~/.ssh/ 目录下新生成两个文件：id_rsa.pub和id_rsa。前者公钥，后者是私钥。

2. 将公钥传送到远程主机host上面

![Alt text](https://github.com/badcyc/dailyLinux/raw/master/ssh/ssh_4.png)

现在就可以通过```ssh user@host```直接登录了

### 其他

1. 查看ssh软件的版本号命令：```$ ssh -V```
![Alt text](https://github.com/badcyc/dailyLinux/raw/master/ssh/ssh_5.png)



 
