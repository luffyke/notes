### 下载安装sonar
```
useradd sonar
passwd sonar
su - sonar
mkdir -p local && cd local
wget https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-6.1.zip
unzip sonarqube-6.1.zip
ln -s sonarqube-6.1 sonarqube
```

### 创建mysql数据库
```
mysql -uroot -p
CREATE DATABASE sonar CHARACTER SET utf8 COLLATE utf8_general_ci;
CREATE USER 'sonar'@'%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON sonar.* TO 'sonar'@'%' WITH GRANT OPTION;
```

### 配置数据库
```
vi ~/local/sonarqube/conf/sonar.properties
```
配置username和password，已去掉url的注释。
```
sonar.jdbc.username=sonar
sonar.jdbc.password=password
sonar.jdbc.url=jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance
```

### 启动
```
cd ~/local/sonarqube
./bin/linux-x86-64/sonar.sh start
```

### 执行分析
#### maven
setting.xml
```
<settings>
    <pluginGroups>
        <pluginGroup>org.sonarsource.scanner.maven</pluginGroup>
    </pluginGroups>
    <profiles>
        <profile>
            <id>sonar</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <!-- Optional URL to server. Default value is http://localhost:9000 -->
                <sonar.host.url>
                  http://myserver:9000
                </sonar.host.url>
            </properties>
        </profile>
     </profiles>
</settings>
```

```
export MAVEN_OPTS="-Xmx1024m"
mvn clean verify sonar:sonar
```
