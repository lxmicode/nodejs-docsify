## Debian 命令和问题

### 机器之间免密码
```bash
# 比如 服务器A，服务器B
# 2台服务器各自执行一次生成证书
ssh-keygen -t rsa

# 把自己的公钥复制到远程服务，2台互相推送一次
# A执行
ssh-copy-id root@B.id
# B执行
ssh-copy-id root@A.id
```

### unzip乱码问题
- win中打包存在中文名字的文件，然后在linux中解压文件名乱码
```bash
unzip -O CP936 your_file.zip
#unzip -O UTF-8 your_file.zip
```

### 美化命令行
```bash
# 完整步骤
apt install -y zsh

sh -c "$(curl -fsSL https://gitee.com/gloriaied/oh-my-zsh/raw/master/tools/install.sh)"

awk '{gsub(/ZSH_THEME="robbyrussell"/, "ZSH_THEME=\"agnoster\""); print}' ~/.zshrc > temp && mv temp ~/.zshrc

echo "export TIME_STYLE='long-iso'" >> ~/.zshrc

source ~/.zshrc

Ubuntu安装
apt install zsh
CentOs安装
yum install zsh
查看版本，是否安装成功的标志
zsh --version
Ubuntu设为默认shell
chsh -s $(which zsh)
CentOs设为默认shell
chsh -s /bin/zsh

方式	命令 -github源
curl	sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
方式	命令 -gitee源
sh -c "$(curl -fsSL https://gitee.com/gloriaied/oh-my-zsh/raw/master/tools/install.sh)"

agnoster
```

### 设置时间
```bash
# 安装ntp
apt update && apt install ntp && systemctl start ntp

# 设置时区
timedatectl set-timezone Asia/Shanghai

# 修改locale vim /etc/default/locale
LANG=en_US.UTF-8
LANGUAGE=
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```

### 查看cron日志
```bash
# debian12版本，开始使用journalctl记录日志，不再使用rsyslog
journalctl -u cron.service -n 50 --no-pager
```

### 升级内核
- [清华镜像源找对应的版本](https://mirrors.tuna.tsinghua.edu.cn/help/debian/)

```bash
# 升级软件包,确保每一步都无异常
sudo apt update
sudo apt upgrade
sudo apt full-upgrade

# 清理不再需要的软件包。
sudo apt autoremove

# 修改软件源列表,都更新完，修改成下一个版本软件源
vim /etc/apt/sources.list

# 更新软件包索引
sudo apt update

# 安装最新的基础软件包
sudo apt upgrade
sudo apt full-upgrade

# 完成所有升级后，请重启系统。
sudo reboot

# 检查系统版本
lsb_release -a
```

### 测下载速度
```txt
# 国内电信 1G
wget https://vipspeedtest8.wuhan.net.cn:8080/download?size=1073741824
# 亚洲 100MB（Vultr）
wget https://sgp-ping.vultr.com/vultr.com.100MB.bin
wget https://sgp.proof.ovh.net/files/1Gb.dat
```


### 开启启动脚本
```bash
# 设置开机启动
systemctl enable rc-local.service
# 启动
systemctl start rc-local.service
# 查看状态, 如果提示/etc/rc.local文件不存在，就创建一个
systemctl status rc-local.service
```

- 文件rc.local例子

```text
#!/bin/sh -e
# 开机启动

```

### 服务测试端口
```bash
# 占用一下端口
nc -lk -p 8080
# 在对应的服务器测试端口是否通
curl -v 
```

### 防火墙问题
- 目前ubuntu遇到过,防火墙有先后顺序的问题
- docker内部容器无法访问宿主机端口
- ufw ufw disable 可以直接先关闭防火墙检查
- ufw enable 启动防火墙
- 或者 ufw allow 8080/tcp 把对应的端口不限制
- 在容器动使用 curl -v 宿主机IP:端口测试
```txt
 1848 74028 DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate INVALID
 251K   19M REJECT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited
    0     0 ACCEPT     tcp  --  *      *       192.168.1.75        0.0.0.0/0            tcp dpt:10086
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:10086

1.75是访问不到本机的， REJECT已经全部拦截， 在REJECT之后的规则都被拦截了
```

### 测试两服务器端口是否通
```bash
# 服务器A占用端口
nc -l -v -p 8888
# 在对应的服务测试端口
telnet ip port
curl -v http://ip:port
```

### 磁盘名称每次重启变化
```bash
# 查看磁盘信息，获取UUID
blkid
# 编辑fstab文件，加入开机启动自动挂载
vim /etc/fstab
# 添加格式
UUID=<UUID> <mount_point> <type> defaults 0 2
例如：
UUID=xxxx-xxxx /mnt/data ext4 defaults 0 2
```
