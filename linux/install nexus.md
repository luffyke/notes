1.下载和解压（root user）
```
wget https://www.sonatype.com/download-oss-sonatype
tar -xzvf nexus.tar.gz
mv nexus /opt/
```

2.添加nexus用户
```
groupadd nexus
useradd -g nexus nexus
```
3.配置
- 编辑.bashrc
```
NEXUS_HOME="/opt/nexus"
```
- 编辑$NEXUS_HOME/bin/nexus.rc
```
run_as_user = "nexus"
```
- 配置服务
```
sudo ln -s $NEXUS_HOEM/bin/nexus /etc/init.d/nexus
chkconfig --add nexus
chkconfig --level 345 nexus on
```
- 修改data目录
```
mkdir /data/repo
mkdir /data/repo/tmp
chown -R nexus:nexus /data/repo/*
```
- 编辑$NEXUS_HOME/bin/nexus.vm
```
-Dkaraf.data=/data/repo
-Djava.io.tmpdir=/data/repo/tmp
```

4.运行nexus
```
service nexus start
or
./nexus run
```
