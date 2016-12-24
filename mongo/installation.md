## mac
mac下直接用brew安装就可以了。
```
brew install mongodb
```
> 参考[官方文档](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)


## linux

> 环境是centos6.5

官网是推荐使用yum安装。

> 参考[官方文档](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/)

### 推荐安装
- 配置yum
创建/etc/yum.repos.d/mongodb-org-3.4.repo
```
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```
- 安装Mongo
```
sudo yum install -y mongodb-org
```
- 默认路径
MongoDB实例默认在**/var/lib/mongo**存储数据文件，在**/var/log/mongodb**放日志文件，运行在**mongod**用户下面。你可以在**/etc/mongod.conf**修改日志和数据文件路径。

- 运行
```
sudo service mongod start
```
mongod装在下面路径
```
/usr/bin/mongod
```
可以通过-f参数传入配置文件
```
/usr/bin/mongod -f mongo.conf
```

- 验证
可以通过下面命令验证
```
sudo service mongod status
```
或者通过日志**/var/log/mongodb/mongod.log**验证
```
[initandlisten] waiting for connections on port <port>
```
<port> 是配置在 /etc/mongod.conf, 默认是27017。

- 开机启动
```
sudo chkconfig mongod on
```

- 停止和重启mongo
```
sudo service mongod stop
sudo service mongod restart
```

### manual安装
> 参考[官方文档](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-linux/)
