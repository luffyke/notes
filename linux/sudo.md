### 介绍
众所周知，类unix系统的root账号是一个超级管理员账号，可以不受限制的执行任何命令。
sudo就是像root账户一样执行命令（use 'sudo' execute command as Root）。

### 如何获得root权限
登录root账户
```
ssh root@host
```
或者切换到root账号（已登录其他账户情况）
```
su - root
```
**sudo**命令是通过/etc/sudoers配置的。

> 永远不要用普通的编辑器修改这个文件，一定要用visudo命令代替！
visudo命令会打开一个普通的文本编辑，但是当修改文件时候它会验证整个文件，这可以避免非法的修改导致无法操作sudo。

```
visudo
```
执行visudo之后大概的/etc/sudoers文件如下：
```
Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

root    ALL=(ALL:ALL) ALL

%admin ALL=(ALL) ALL
%sudo   ALL=(ALL:ALL) ALL

#includedir /etc/sudoers.d
```
第一行```Defaults env_reset```, 重置终端环境移除所有用户变量，这是一个安全措施用来清理潜在的来自sudo会话的有害环境变量。

第二行```Defaults mail_badpa```, 告诉系统发邮件通知给配置的mailto用（默认发给root账户）有错误的sudo密码操作。

第三行, ```Defaults secure_path=...```开始部分, 标明了sudo操作会使用的PATH，这主要是防止使用可能有害的用户环境。

第四行, 规定了root用户的sudo权限。
```
root ALL=(ALL:ALL) ALL
```
第一个参数标识了规则应用的用户名。
```
demo ALL=(ALL:ALL) ALL
```
第一个"ALL"标识这条规则应用到所有的主机（hosts）。
第二个"ALL"标识demo用户可以运行所有用户的命令。
第三个"ALL"标识demo用户可以运行所有组的命令。
第四个"ALL"标识这条规则应用到所有命令。

### 示例
1. 可以像上面那样配置一个demo ALL=(ALL:ALL) ALL使得demo用户获得root权限。
2. 将用户添加到具有root权限的组。
ubuntu 16.04
```
sudo usermod -aG sudo username
```
centos
```
sudo usermod -aG wheel username
```
在centos，如果添加用户到wheel没起作用，那就需要编辑/etc/sudoers文件去掉下面注释。
```
%wheel ALL=(ALL) ALL
```
