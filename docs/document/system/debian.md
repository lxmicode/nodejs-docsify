## Debian 命令和问题

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
