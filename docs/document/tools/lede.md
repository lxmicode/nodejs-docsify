## Lede中的常用软件


### 网络共享-samba

- 登陆路由器-网络存储-网络共享-编辑模板

#注释点下面代码   
#invalid users = root

- 设置用户/密码(不设置为匿名)

```bash
$ smbpasswd -a root
# 输入 123456
```

- 共享目录

| 名称 | 目录 |  允许用户  |只读  |可预览  |允许匿名用户|创建权限掩码|目录权限掩码|
|-------| -------| -------| -------| -------| -------| -------| -------|
| gd      | /mnt/gd   |   root     ||√||0775|0775|

- 重启路由器完成

- windows 检测是否开启smb协议
- windows 打开网络，找到共享目录输入上面设置的账号密码

- linux 挂载
- 命令
```bash
#此处匿名方式
mount -t cifs //192.168.1.5/boot /mnt/lede -o username=root,password=,vers=1.0
```

- 可能遇到的问题
  - 查看日志
  ```bash
  tail -f  /var/log/kern.log
  ```
  - 未指定版本
  ```txt
  debian kernel: [1984525.829579] No dialect specified on mount. Default has changed to a more secure dialect, SMB2.1 or later (e.g. SMB3), from CIFS (SMB1). To use the less secure SMB1 dialect to access old servers which do not support SMB3 (or SMB2.1) specify vers=1.0 on mount.
  #解决：mount 命令添加 vers=1.0
  ```
  - 其它错误
  ```txt
  cifs_mount failed w/return code = -22
  cifs_mount failed w/return code = -6
  #解决
  # 1.可能问题是smb目录没设置对，先检查smb设置名称钱前不要带斜杆<del>/boot</del>
  # 2.如果有windows系统，测试是否能正常打开
  ```

  
  
  
