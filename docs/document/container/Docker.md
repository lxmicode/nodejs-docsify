## 镜像命令
###  `search` 
- 查询镜像
```docker
sudo docker search 镜像名
docker search centos
```

### `pull` 
- 拉取镜像
```docker
docker pull 镜像名:版本(默认最新)
docker pull centos:latest
```

### `run` 
- 创建容器并运行
```docker
docker pull 镜像名:版本(默认最新)
docker pull centos:latest
```

###   `commit` 
- 提交镜像
```docker
docker commit 镜像ID/镜像名称
```

## 容器命令
###  `ps` 
- 查看当前容器
- -a 查看历史
```docker
sudo docker ps
sudo docker ps -a
```

###  `start` 
- 查看当前容器

###  `stop` 
- 停止

###  `rm` 
- 删除
