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
