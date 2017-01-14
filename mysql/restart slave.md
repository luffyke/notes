- stop mysql slave
```
stop slave;
```

- dump mysql master
```
mysqldump -uroot -p --master-data=2 --single-transaction demo > /data/mysql/demo.sql
tar -czvf demo.tar.gz demo.sql
scp demo.tar.gz yourname@yourmasterhost:/tmp
```

- get MASTER_LOG_POS
```
grep MASTER_LOG_POS demo.sql
```
output: master-bin.000053 and 134861566(these values for next step)


- import slave
```
tar -xzvf demo.tar.gz
mysql -uyourname -p -hyourmasterhost demo < demo.sql
```

- restart mysql slave
```
reset slave;
CHANGE MASTER TO
MASTER_HOST='yourslavehost',
MASTER_USER='yourrepluser',
MASTER_PASSWORD='yourpassword',
MASTER_LOG_FILE='master-bin.000053',
MASTER_LOG_POS=134861566;
start slave;
```
