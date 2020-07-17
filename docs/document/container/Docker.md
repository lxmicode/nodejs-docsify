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
```

### `image` 
- 查看所有镜像
```docker
docker image
```

### `run` 
- 创建容器并运行
```docker
sudo docker run 镜像名:版本(默认最新)
sudo docker run centos:latest
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

## 网络配置(容器之间)
默认容器之间无法通讯，需指定端口 -p/-P参数
###  `-p` 
- 指定对外端口
```docker
sudo docker run -p 本机端口:容器端口 镜像名
```
- 例子，后台创建并启动一个叫web的nginx容器，对外端口80，内部端口80
```docker
sudo docker run  -d -p 80:80 --name web nginx
```

###  `--link` 
- 容器之间交互
```docker
sudo docker run  --link 容器名:连接名
```
- 例子，先创建一个DB容器，然后创建一个web容器连接DB容器。
- 从此DB容器启动并没有使用-P/-p映射端口，两者之间创建了一个安全隧道，避免端口暴露
```docker
sudo docker run  -d --name db mysql
sudo docker run  -d --link db:mysqlLink --name web nginx
```

###  `port` 
- 查看容易映射端口信息
```docker
sudo docker port 镜像id/别名
```

























