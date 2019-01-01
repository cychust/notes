# git 多用户配置教程

1. 在~/.ssh文件夹目录下创建一个config文件.

我的配置文件如下:

```
# first git user
Host badcyc.github.com
HostName github.com
User git
IdentityFile ~/.ssh/id_rsa

# second

Host cychust.github.com
HostName github.com
User git
IdentityFile ~/.ssh/id_rsa_hust
```

其中host不能一样，下面会用到。
IdentityFile 是你创建的秘钥文件

2. 多用户的使用

如原地址是git@github.com:cychust/labs.git 则需要替换为
cychust.github.com:cychust/labs.git.
其规则是从上至下读取config的内容，在每个Host下寻找对应的私钥。

如 git remote add origin cychust.github.com:cychust/labs.git

3. 测试

```
ssh -T badcyc.github.com
ssh -T cychust.github.com
```

会出现Hi badcyc! You've successfully authenticated, but GitHub does not provide shel
l access.
的结果


