

## CMD 命令

### 查看端口占用(主要PID列)
```bash
netstat -aon|findstr "端口号"
netstat -aon|findstr "8999"
```
结果
```txt
  TCP    127.0.0.1:8998         127.0.0.1:8999         ESTABLISHED     7724
  TCP    127.0.0.1:8999         127.0.0.1:8998         ESTABLISHED     7724
```


### 根据线程编号(PID)查询使中的任务
```bash
tasklist|findstr "7724"
```
结果
```txt
idea64.exe                    7724 Console                    2  2,672,808 K
```

### 根据线程编号(PID)结束进程
```bash
TASKKILL /PID  7724
```




