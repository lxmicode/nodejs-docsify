## Debian 命令和问题

### 查看cron日志
```bash
# debian12版本，开始使用journalctl记录日志，不再使用syslog
journalctl -u cron.service -n 50 --no-pager
```
