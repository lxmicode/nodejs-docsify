## 镜像命令
###  `镜像加速`


## 设置开机启动
###  `centos`
```bash
$ sudo systemctl enable docke
```


## 镜像命令
### `log命令`
```
# 查看40分钟内的日志,根据关键字
docker logs --since 40m 镜像名或ID | grep "关键字"
```

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

## 实际中应用
### 备份数据库
```bash
# 后台运行备份数据库
nohup docker exec my_mysql sh -c 'exec mysqldump -u root -p"123" --single-transaction test_db blade_code' > test_db.sql 2> /dev/null &

# 还原数据库
cat test_db.sql | docker exec -i my_mysql sh -c 'exec mysql -u root -p"123" test_db2'

```

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
- 容器之间交互,两者之间创建了一个安全隧道，避免端口暴露
```docker
sudo docker run  --link 容器名:连接名
```
- 例子
创建一个alpine容器，安装mysql客户端链接DB容器
```docker
##创建DB容器
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

### `up 启动`
默认启动docker-compose.yml

```docker
sudo docker-compose up
## -f 指定配置文件
sudo docker-compose up  -f 文件名
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
    ports: #端口
      - 80:80
    volumes: ##容器卷
      - /root/nginx/config/nginx.conf:/etc/nginx/nginx.conf
      - /root/nginx/html:/usr/share/nginx/html
```

## 迁移数据目录
- 起因：系统磁盘满，挂载新磁盘到 /app

```bash
# 确认当前 Docker 数据存储路径
docker info | grep "Docker Root Dir"

# 停止 Docker 服务在修改配置之前，需要先停止 Docker 服务，以确保数据一致性：
sudo systemctl stop docker

# 迁移现有数据到新目录 
# 将现有的 Docker 数据从默认目录（如 /var/lib/docker）迁移到新的目标目录（如 /app/docker）。可以使用 rsync 或 cp 命令完成数据迁移。
# 使用 rsync 迁移数据： -a：保留文件属性和权限。 -P：显示进度并支持断点续传。
sudo rsync -aP /var/lib/docker/ /app/docker/

# 验证数据完整性：
# 迁移完成后，检查 /app/docker 目录是否包含所有必要的文件和子目录。
# 修改 Docker 配置文件
# Docker 的配置文件通常位于 /etc/docker/daemon.json。如果没有该文件，可以手动创建。

# 编辑或创建 /etc/docker/daemon.json 文件：
# 添加以下内容，指定新的数据存储路径：
# json

{
  "data-root": "/app/docker"
}


# 重启 Docker 服务
# 完成配置后，重新启动 Docker 服务以应用更改：
sudo systemctl start docker

# 验证配置是否生效 再次运行以下命令，确认 Docker 的数据存储路径已更改为 /app/docker：
docker info | grep "Docker Root Dir"

# 清理旧数据（可选）
# 如果迁移成功且 Docker 正常运行，可以删除旧的 /var/lib/docker 目录以释放空间：
sudo rm -rf /var/lib/docker

# 存在问题：挂载目录没变
# 解决办法1： 重启电脑
# 解决办法2： 重启对应的服务器
```


## 相关其它
### alpine
Alpine Linux Docker 镜像基于 Alpine Linux 操作系统，后者是一个面向安全的轻型 Linux 发行版。Alpine Linux Docker 镜像也继承了 Alpine Linux 发行版的这些优势。相比于其他 Docker 镜像，它的容量非常小，仅仅只有 5M，且拥有非常友好的包管理器。

- nginx:alpine
- redis:alpine

## 注意
### 代理宿主机
- ifconfig或者ipaddr 获取宿主机IP，然后再nginx代理使用ip,不能使用localhost/127
```bash
#debian
ipaddr 中 ens3 inet:ip 参数
#其它系统
ifconfig 中 eth0 inet:ip参数
```

##  常用脚本
### `查看日志`
```bash
#!/bin/bash

# 默认参数（"-" 表示未填写）
cntr_name="-"
time="-"
keyword="-"
start_time="-"
end_time="-"

# 参数解析（支持命令行参数）
while [[ ${#} -gt 0 ]]; do
  case "${1}" in
    -n|--name)     cntr_name="${2}"; shift 2 ;;
    -t|--time)     time="${2}";      shift 2 ;;
    -k|--keyword)  keyword="${2}";   shift 2 ;;
    --start)       start_time="${2}";shift 2 ;;
    --end)         end_time="${2}";  shift 2 ;;
    *) echo "未知参数: ${1}"; exit 1 ;;
  esac
done

# ================================================
# 校验必填参数
# ================================================
if [ "${cntr_name}" = "-" ]; then
  echo "错误：必须指定容器名或容器ID（-n/--name）"
  exit 1
fi

# ================================================
# 处理时间参数
# ================================================
SINCE=""
UNTIL=""

# 检查是否所有时间参数都未填写
if [ "${time}${start_time}${end_time}" = "---" ]; then
  SINCE="--since 5m"  # 默认查询最近5分钟
else
  # 处理 time 参数（相对时间）
  if [ "${time}" != "-" ]; then
    if [[ "${time}" =~ ^[0-9]+[mhd]$ ]]; then
      SINCE="--since ${time}"
    else
      echo "错误：time 参数格式错误（示例：5m/2h/3d）"
      exit 1
    fi
  fi

  # 处理 start_time 和 end_time（绝对时间）
  if [ "${start_time}" != "-" ] || [ "${end_time}" != "-" ]; then
    # 辅助函数：转换日期格式
    parse_date() {
      local input="${1}"
      local type="${2}"
      local parsed_date

      # 检查是否为完整 ISO 时间戳（yyyy-mm-ddThh:mm:ss）
      if [[ "${input}" =~ ^[0-9]{4}-[0-9]{2}-[0-9]{2}T[0-9]{2}:[0-9]{2}:[0-9]{2}$ ]]; then
        # 补充本地时区信息
        parsed_date=$(date -d "${input}" +"%Y-%m-%dT%H:%M:%S%z" 2>/dev/null)
      # 检查是否为仅日期（yyyy-mm-dd）
      elif [[ "${input}" =~ ^[0-9]{4}-[0-9]{2}-[0-9]{2}$ ]]; then
        if [ "${type}" = "start" ]; then
          parsed_date=$(date -d "${input}T00:00:00" +"%Y-%m-%dT%H:%M:%S%z" 2>/dev/null)
        else
          parsed_date=$(date -d "${input}T23:59:59" +"%Y-%m-%dT%H:%M:%S%z" 2>/dev/null)
        fi
      else
        echo "错误：时间格式无效（支持：yyyy-mm-dd 或 yyyy-mm-ddThh:mm:ss）"
        exit 1
      fi

      # 检查解析是否成功
      if [ -z "${parsed_date}" ]; then
        echo "错误：无法解析时间 '${input}'"
        exit 1
      fi

      echo "${parsed_date}"
    }

    # 处理 start_time
    if [ "${start_time}" != "-" ]; then
      parsed_start=$(parse_date "${start_time}" "start")
      SINCE="--since ${parsed_start}"
    fi

    # 处理 end_time
    if [ "${end_time}" != "-" ]; then
      parsed_end=$(parse_date "${end_time}" "end")
      UNTIL="--until ${parsed_end}"
    fi
  fi
fi

# ================================================
# 构建日志命令
# ================================================
# 查找容器ID（支持模糊匹配）
CONTAINER_ID=$(docker ps -a --filter "name=${cntr_name}" --format "{{.ID}}")
if [ -z "${CONTAINER_ID}" ]; then
  echo "错误：未找到容器 '${cntr_name}'"
  exit 1
fi

LOG_CMD="docker logs ${SINCE} ${UNTIL} ${CONTAINER_ID} 2>&1"

# 添加关键字过滤
if [ "${keyword}" != "-" ]; then
  LOG_CMD="${LOG_CMD} | grep -i --color=auto '${keyword}'"
fi

# ================================================
# 执行查询
# ================================================
echo "查询容器 ${CONTAINER_ID} 日志..."
echo "时间范围：${SINCE} ${UNTIL}"
echo "关键字：${keyword}"
echo "---------- 日志内容 ----------"
eval ${LOG_CMD}
```

## 练习
### nginx
- 拉取最新镜像，复制配置到本地
```bash
docker pull nginx:latest
docker run --name nginx-test -p 8080:80 -d nginx 
docker container cp nginx-test:/etc/nginx ~/app/nginx
docker rm -f nginx-test
```
### 使用 compose link通讯(同yml)
- 创建yaml文件,重点在于links参数，创建后web1可以通过 web2代替IP操作（es: http://web2）
```yaml
version: '3.8'
services:
  ##web nginx1服务器
  web1:
    image: nginx:alpine
    container_name: nginx1
    ports:
      - 80:80
    links:
      - web2 ##重点，名字为配置中的服务名(services:web2)

  ##web nginx2服务器
  web2:
    image: nginx:alpine
    container_name: nginx2
    ports:
      - 80:80
```

### 使用 compose network通讯(同yml)
- 创建yaml文件,重点在于links参数，创建后web1可以通过 web2代替IP操作（es: http://web2）
```yaml
version: '3.8'
services:
  ##web nginx1服务器
  web1:
    image: nginx:alpine
    container_name: nginx1
    ports:
      - 80:80
    networks:
      - web-net ##网络名

  ##web nginx2服务器
  web2:
    image: nginx:alpine
    container_name: nginx2
    ports:
      - 80:80
    networks:
      - web-net
      
##创建网络 web-net网络，默认驱动：bridge      
networks:
 web-net:
```

### 使用 compose network通讯(不同yml)
- 创建yml1文件
```yaml
version: '3.8'
services:
  web:
    image: nginx:alpine
    container_name: nginx
    ports:
      - 80:80
    networks:
      - web-net ##设置网络

networks:
  web-net: ##网络名

```

- 创建yml2文件，然后yml2桥接yml1,桥接后可以使用服务名web进行操作
```yaml
version: '3.8'
services:
  web2:
    image: nginx:alpine
    container_name: nginx2
    ports:
      - 80:80
    networks:
      - web_web-net (重点)

networks:
  web_web-net: ##yml中创建的网络，可通过 docker network ps查看
    external: true ## 使用已存在网络
```

### mysql 搭建练习
- 搭建Mysql 设置管理密码和添加一个管理员账号，MYSQL_USER 默认管理员权限

```shell
docker run --name db \
 -p 3306:3306 \
 -e MYSQL_ROOT_PASSWORD=管理员密码(必选) \
 -e MYSQL_USER=新用户(可选) \
 -e MYSQL_PASSWORD=新用户密码(可选) \
 -itd mysql:5.7.31 \
```

### 查看容器启动时的命令
- 因没有使用编排，时间长了，忘记启动时候参数

```shell
# 借用python
pip install runlike
# 不换行
runlike <container-name>
# 换行
runlike -p <container-name>
```

### 修改镜像源
```bash
cd /etc/docker
wget https://gitee.com/iicode/my_sharecode/raw/master/daemon.json
sudo systemctl daemon-reload
sudo systemctl restart docker
```





