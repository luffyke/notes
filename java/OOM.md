## OOM

### 什么是OOM
OOM是OutOfMemoryError的缩写，意思是内存溢出错误。

常见错误：
JVM Heap（堆）溢出：java.lang.OutOfMemoryError: Java heap space，这是最常用的内存溢出错误，当应用需要添加数据到堆内存，而堆内存达到阈值（JVM通过-Xmx设置最大的堆内存大小）的时候抛出这个错误。

### 原因
1. 内存中加载的数据量过于庞大，如一次从数据库取出过多数据
2. 集合类中有对对象的引用，使用完后未清空，使得JVM不能回收
3. 代码中存在死循环或循环产生过多重复的对象实体
4. 启动参数内存值设定的过小

### 排查
1. dump heap
```
jmap -dump:format=b,file=dump.bin PID
jhat -port 5000 dump.bin 或者 使用Eclipse MAT分析堆内存
```

2. 查看gc日志

### 优化
jvm启动参数
```
-server -Xms3800m -Xmx3800m -Xmn1500m -Xss256k -XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:CMSFullGCsBeforeCompaction=5 -XX:+UseCMSCompactAtFullCollection -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=60 -XX:+CMSClassUnloadingEnabled -XX:+DisableExplicitGC -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/home/user/logs -Xloggc:/home/user/logs/gc.log
```