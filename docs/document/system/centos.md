## CenteOS 命令和问题
### 服务管理
- Linux中如何启动、重启、停止、重载服务以及检查服务（如 httpd.service）状态 
```bash
# 启动
systemctl start httpd.service
# 重启
systemctl restart httpd.service
# 停止
systemctl stop httpd.service
# 重载服务
systemctl reload httpd.service
# 检查服务状态、配置等信息
systemctl status httpd.service
# 激活服务并在开机时启用
systemctl is-active httpd.service
# 开机时启用
systemctl enable httpd.service
# 禁用服务
systemctl disable httpd.service
```

### 用户管理

- 设置用户口令：
```bash
passwd [<用户账号名>]
```

- 禁用用户账户口令
```bash
passwd -l <用户账号名>
```

- 查看用户账户口令状态
```bash
passwd -S <用户账号名>
```

- 恢复用户账户口令
```bash
passwd -u <用户账号名>
```

- 清除用户账户口令
```bash
passwd -d <用户账号名>
```

- cannot open /etc/passwd 问题解决
```bash
chattr -i /etc/passwd
chattr -i /etc/shadow
```

### crontab 定时任务
#### 注意：crond $PATH默认:/usr/bin:/bin
```bash
##编辑，命令操作与vi基本一致，修改后需重启
crontab  -e
##重启
service  crond restart
```

#### crontab 日志查看
```bash
## 从邮件查看详情，执行过程、控制台日志
less /var/spool/mail/root
## 日志文件只能查看是否执行
tail -f /var/log/cron
```

#### 找不到路径问题
1. 使用export 导入变量
```bash
##导入指定Bin目录
1 * * * * export PATH=/xx/node/bin:$PATH; +命令
```

2. 直接指定执行文件
```bash
1 * * * * /usr/local/bin/node test.js
```

#### 时间/日志时间显示 问题
```bash
#设置时区
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
#设置时区
timedatectl set-timezone Asia/Shanghai
#同步时间
ntpdate pool.ntp.org
#重启日志服务
service rsyslog restart
```

###  `ISO做本地源`
- 镜像版本:CentOS-6.10-x86_64
- 需下DVD1和DVD2 2个合并才能完整
>阿里FTP：http://mirrors.aliyun.com/centos/6.10/isos/x86_64/

1. 上传CentOS到服务器，并挂载CentOS镜像
```shell
##创建目录
mkdir -p /mnt/dvd1 /mnt/dvd2 /mnt/dvd
##挂载ISO镜像
mount -o loop /media/CentOS-6.10-x86_64-bin-DVD1.iso /mnt/dvd1
mount -o loop /media/CentOS-6.10-x86_64-bin-DVD2.iso /mnt/dvd2
```

2. 拷贝并合并文件
```shell
##拷贝dvd1镜像的内容到/mnt/dvd
cp  -r  /mnt/dvd1/*  /mnt/dvd
##将第dvd2的Packages目录下的rpm文件合并dvd
cp  -r  /mnt/dvd2/Packages/*.rpm  /mnt/dvd/Packages/
```

3. 合并TRANS.TBL
```shell
##将DVD1和DVD2中的TRANS.TBL合并并排序
cat /mnt/dvd[12]/Packages/TRANS.TBL|sort > /mnt/dvd/Packages/TRANS.TBL
```

4. 备份YUM配置文件
```shell
##进入yum仓库配置目录
cd /etc/yum.repos.d
##创建备份目录，复制备份文件
mkdir bakup
cp *.repo ./bakup
```

5. 创建YUM配置文件
```shell
vi /etc/yum.repos.d/CentOS-Media.repo
[media-repo]
name=this is a media iso repo
baseurl=file:///mnt/dvd
gpgcheck=0
enabled=1
gpgkey=file:///mnt/dvd/RPM-GPG-KEY-CentOS-6
```

6. 检查仓库配置是否成功，正常通过命令后会显示配置后的仓库信息
```shell
$ sudo yum repolist all
```

7. 更新YUM源
```shell
$ sudo yum clean all
$ sudo yum upgrade
```
8. 遇到问题error: cannot open Packages database in /var/lib/rpm
```shell
##原因是RPM数据库被破坏
##重建数据库后恢复正常：
cd /var/lib/rpm/
for i in `ls | grep 'db.'`;do mv $i $i.bak;done
rpm --rebuilddb
yum clean all
```
>https://blog.51cto.com/alsww/1742134
>

### 批量结束进程
```shell
kill -9 `ps -ef |grep xxx|awk '{print $2}' `
```
 
## 相关问题
### 解决yum update出错"package is a duplicate with"

```bash
#错误信息
Error: Package: glibc-2.12-1.212.el6_10.3.i686 (updates_6)
           Requires: glibc-common = 2.12-1.212.el6_10.3
           Installed: glibc-common-2.17-55.el6.x86_64 (installed)
               glibc-common = 2.17-55.el6
           Available: glibc-common-2.12-1.149.el6.x86_64 (base)
               glibc-common = 2.12-1.149.el6
           Available: glibc-common-2.12-1.209.el6.x86_64 (base_6)
               glibc-common = 2.12-1.209.el6
           Available: glibc-common-2.12-1.209.el6_9.1.x86_64 (updates_6)
               glibc-common = 2.12-1.209.el6_9.1
           Available: glibc-common-2.12-1.209.el6_9.2.x86_64 (updates_6)
               glibc-common = 2.12-1.209.el6_9.2
           Available: glibc-common-2.12-1.212.el6.x86_64 (base_6)
               glibc-common = 2.12-1.212.el6
           Available: glibc-common-2.12-1.212.el6_10.3.x86_64 (updates_6)
               glibc-common = 2.12-1.212.el6_10.3
 You could try using --skip-broken to work around the problem
** Found 3 pre-existing rpmdb problem(s), 'yum check' output follows:
glibc-2.12-1.149.el6.i686 has missing requires of glibc-common = ('0', '2.12', '                                                                                                             1.149.el6')
glibc-2.17-55.el6.x86_64 is a duplicate with glibc-2.12-1.149.el6.i686
pam-devel-1.1.1-24.el6.x86_64 has missing requires of pam = ('0', '1.1.1', '24.e                                                                                                             l6')

## 查询有问题依赖包 glibc
 rpm -qa | grep glibc
glibc-2.17-55.el6.x86_64
glibc-devel-2.17-55.el6.x86_64
glibc-2.12-1.149.el6.i686
glibc-common-2.17-55.el6.x86_64
glibc-headers-2.17-55.el6.x86_64

## glibc-2.17-55.el6.x86_64/glibc-2.12-1.149.el6.i686 重复
## 删除 glibc-2.12-1.149.el6.i686
## yum remove glibc-2.12-1.149.el6.i686
## 重新更新 yum update 
```

 
## 引用和声明
- 代码和例子来源网上收集整理
