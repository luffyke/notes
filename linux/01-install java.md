###下载安装
####使用root用户和切换到/usr/local目录
```
su - root
cd /usr/local
```
####下载和解压
```
wget http://download.oracle.com/otn-pub/java/jdk/8u112-b15/jdk-8u112-linux-x64.tar.gz
tar -xzvf jdk-8u112-linux-x64.tar.gz
rm jdk-8u112-linux-x64.tar.gz
```
####创建软链接
```
ln -s jdk1.8.0_112/ java
```

###配置
```
vim /etc/profile
```
在最后面添加下面命令：
```
JAVA_HOME=/usr/local/java
PATH=$PATH:$JAVA_HOME/bin
export JAVA_HOME PATH
```
运行source更新配置
```
source /etc/profile
```
验证
```
java -version
java version "1.8.0_112"
Java(TM) SE Runtime Environment (build 1.8.0_112-b15)
Java HotSpot(TM) 64-Bit Server VM (build 25.112-b15, mixed mode)
```

###升级
只需要下载最新的tar包，然后重新把软链接指向新包就可以了。
