### 创建用户
```
useradd jenkins
passwd jenkins
su - jenkins
```

### 下载
```
mkdir -p local && cd local
wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war
mv jenkins.war jenkins-lts-2.32.3.war
```

### 启动
```
cd ~ && mkdir -p logs
nohup java -jar jenkins-lts-2.32.3.war --httpPort=8001 >> ~/logs/jenkins.log &
```
