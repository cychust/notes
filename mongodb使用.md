## mongodb使用

命令

```bash
sudo systemctl enable mongod
sudo service mongnod restart
sudo mongod --dbpath /usr/data/db 
sudo mongod --dbpath /usr/data/db --fork --logpath /usr/data/mongodb.log
```



> Failed to restart mongo.service: The name org.freedesktop.PolicyKit1 was not provided by any .service files
>
> 运行命令
>
> ```bash
> sudo systemctl enable mongod
> ```
>
> 



下载

```bash
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list
sudo apt-get update
sudo apt-get install -y mongodb-org
```



下载指定版本

```bash
sudo apt-get install -y mongodb-org=3.6.0 mongodb-org-server=3.6.0 mongodb-org-shell=3.6.0 mongodb-org-mongos=3.6.0 mongodb-org-tools=3.6.0
```

完全卸载

```bash
sudo service mongod stop 
sudo apt-get purge mongodb-org* 
sudo rm -r /var/log/mongodb 
sudo rm -r /var/lib/mongodb
```

配置文件 配置文件在 `/usr/local/etc/mongod.conf` 中 