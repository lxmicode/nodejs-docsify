## CenteOS 命令和问题
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
cp  -r  /mnt/dvd1  /mnt/dvd
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

## 引用和声明
- 代码和例子来源网上收集整理
