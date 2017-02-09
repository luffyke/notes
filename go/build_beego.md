```
#! /bin/bash

appconf="conf/app.conf"

echo "Please enter your deploy environment (dev/prod):"
read -r env
if [ $env != "dev" ] && [ $env != "prod" ]; then
    echo "Please input 'dev' or 'prod'"
    exit
fi
runmode=`cat $appconf | grep runmode | awk -F " " '{print $3}'`
if [ $env != $runmode ]; then
    sed -i'.bak' "s:runmode = $runmode:runmode = $env:g" $appconf
fi
appname=`cat $appconf | grep appname | awk -F " " '{print $3}'`
pid=`ps -ef | grep $appname | grep -v grep | awk '{print $2}'`
if [ ! -z "$pid" ]; then
  echo "kill $pid"
  kill $pid
fi

echo "Building"
go build

if [ ! -d "logs" ]; then
    mkdir -p logs
fi
nohup ./$appname >> logs/app.log &

pid=`ps -ef | grep $appname | grep -v grep | awk '{print $2}'`
echo "Done, pid is $pid"
```
