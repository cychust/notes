# git 代理设置

### http(s)走代理
```
git config --global http.proxy 'socks5://127.0.0.1:1080'
git config --global https.proxy 'socks5://127.0.0.1:1080'
```

上面的代码是进行http进行走代理，若用ssh形式上传则不会走代理。

```
git remote add origin https://github.com/badcyc/.....
```

添加时如上面的一行代码

