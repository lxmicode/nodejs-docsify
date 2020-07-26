## 镜像命令
###  `镜像加速` 


## 设置开机启动
###  `centos`
```bash
$ sudo systemctl enable docke
```


## 镜像命令
###  `search 查询镜像` 
- 查询镜像
```docker
sudo docker search 镜像名
sudo docker search centos
```

### `pull 拉取镜像` 
- 拉取镜像
```docker
docker pull 镜像名:版本(默认最新)
```

### `image 查看镜像` 
- 查看所有镜像
```docker
docker image
```

### `run 创建并运行` 
- 创建并运行容器
```docker
sudo docker run 镜像名:版本(默认最新)
sudo docker run centos:latest
```

###   `commit 提交` 
- 提交镜像
```docker
docker commit 镜像ID/镜像名称:版本
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

###  `update 更新` 
更新容器
- --restart docker启动容器启动
```docker
$ docker update --restart=always <CONTAINER ID>
```


## 数据管理
###  `容器卷` 
- 供容器使用的特殊目录，立即生效，更新不影响镜像，一直存在直到没有容器使用
- 官方推荐
` docker volume COMMAND`
`Commands:`
`create      创建`
 `inspect     查看详情`
 `ls          查看所有`
 `prune       删除未使用`
 `rm          删除`

- 例子
创建一个容器卷,并创建一个nginx容器挂载html页面
```docker
sudo docker volume create test_vol
sudo docker run -d --mount src=test_vol,dst=/usr/share/nginx/html nginx
```
- 命令-旧
`VOLUME ["<路径1>", "<路径2>"...]  或 -v 主机目录:容器目录`
- 例子
后台创建并启动一个叫web的nginx容器，并设置一个容器卷
/var/log/nginx 对应主机  /root/nginx/logs 目录，用于查看nginx日志
```docker
sudo docker run  -d  -v /root/nginx/logs:/var/log/nginx  --name web nginx
```

- 例子
nginx中创建2个目录，至于主机目录随机，通过inspect 命令查看Mounts配置
```docker
sudo docker run  -d   volume["containerDir1","containerDir2"]  --name web nginx
```

###  `--volumes-from 数据卷容器`
数据需要在容器之间共享
- 命令
`docker run --volumes-from [container name]`

- 例子
创建voldatas数据卷容器，app1引用达到共享目的
```docker
$ docker run -name voldatas -v 主机目录:容器目录 -itd  centos
$ docker run --volumes-from voldatas  -name app1 -itd   centos
```

## 容器交互
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

###  `--link 容器间连接` 
- 容器之间交互
```docker
sudo docker run  --link 容器名:连接名
```
- 例子
先创建一个DB容器，然后创建一个alpine容器安装mysql客户端连接DB容器。
从此DB容器启动并没有使用-P/-p映射端口，两者之间创建了一个安全隧道，避免端口暴露
```docker
sudo docker container run -d --name db -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
$ sudo docker container run -itd --name testlink --link db:db_link alpine
##进去testlnk容器
$ sudo docker container exec -it testlnk sh
##安装mysql客户端
$ apk add --no-cache mysql-client
##链接数据库
$ mysql -h db_link -uroot -p 123456
```

###  `port 端口信息` 
- 查看容易映射端口信息
```docker
sudo docker port 镜像id/别名
```

## Docker Compose
Compose 是用于定义和运行多容器 Docker 应用程序的工具
默认名:docker-compose.yml
### `-f 指定配置文件`
```docker
sudo docker-compose -f 文件名
```

###  `Dockerfile 容器配置`

###  `Dockerfile Compose`
配置 `
- 简单配置
```yaml
version: "3"
services:
  web:
    image: nginx #镜像名
    container_name: web #容器名称
    porst: #端口
      - 80:80
    volumes: ##容器卷
      - /root/nginx/config/nginx.conf:/etc/nginx/nginx.conf
      - /root/nginx/html:/usr/share/nginx/html
```

## 相关其它
### alpine
Alpine Linux Docker 镜像基于 Alpine Linux 操作系统，后者是一个面向安全的轻型 Linux 发行版。Alpine Linux Docker 镜像也继承了 Alpine Linux 发行版的这些优势。相比于其他 Docker 镜像，它的容量非常小，仅仅只有 5M，且拥有非常友好的包管理器。


## 练习
### 使用 compose 配置 nginx
1. 宿主机目录准备
- 	/root/nginx/config/nginx.conf nginx配置文件
- 	/root/nginx/html html目录
- 	/root/nginx/logs  日志目录

2. 获取nginx.conf配置文件 到  /root/nginx/config/nginx.conf
```shell
$ sudo docker run --name web -d nginx
$ sudo docker cp web:/etc/nginx/nginx.conf /root/nginx/config/nginx.conf
$ sudo docker rm -f web
```
3. 创建并启动Nginx
```shell
$ sudo docker run --name web\
-p 80:80 \
-v /root/nginx/config/nginx.conf:/etc/nginx/nginx.conf \
-v /root/nginx/html:/usr/share/nginx/html \
-v /root/nginx/logs:/var/log/nginx \
-itd nginx \;
```

### mysql 搭建练习
- 搭建Mysql 设置管理密码和添加一个管理员账号，MYSQL_USER 默认管理员权限 
```shell
docker run --name my1  \
-p 3306:3306 \
-e MYSQL_ROOT_PASSWORD=管理员密码 \
-e MYSQL_USER=新用户 \
-e MYSQL_PASSWORD=新用户密码 \
-itd mysql:5.7.31 \
```











