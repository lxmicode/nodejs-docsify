## 谷歌云盘
### 申请团盘


### gd-utils docker版本
- 准备域名或公网IP，确保https能正常使用
- 准备gd-utils 中的config.js 文件，用于覆盖docker中的对应文件
(可选，可以在docker中直接修改配置)
- 准备SA文件
- 安装docker环境参考 [docker安装](https://docs.docker.com/engine/install/centos/)
- 安装 gd-utils-docker 镜像
```bash
#安装镜像
docker pull gdtool/gd-utils-docker
#启动镜像 
## 23333端口: gd-utils机器人，这里为了方便直接443
## 4200端口: webshell
## 80: 文件管理界面，默认不启动 启动方式 cd / && filebrowser &,账号密码:admin
sudo docker run --restart=always  -d \
 -e USERPWD="your_self_passsword" \
 -p 4200:4200 \
 -p 80:80 \
 -p 443:23333 \
 --name gd-utils \
 gdtool/gd-utils-docker
 ## 复制config.js 到镜像（或进去gd-utils容器直接修改配置文件）
 sudo docker cp /root/gd-utils/config.js gd-utils:/gd-utils
 ## 复制sa 到镜像
 sudo docker cp /root/gd-utils/sa/* gd-utils:/gd-utils/sa
 ##重启
 sudo docker restart gd-utils
 ##设置机器人
curl -F "url=[YOUR_WEBSITE]/api/gdurl/tgbot" 'https://api.telegram.org/bot[YOUR_BOT_TOKEN]/setWebhook'
```

### aria2离线+自动上传


## 参考
> [gd-utils](https://github.com/iwestlin/gd-utils)
> [gd-utils-docker](https://github.com/gdtool/gd-utils-docker)
> [搬山之术](https://tech.he-sb.top/posts/usage-of-gclone/)
>  [AutoRclone](https://tech.he-sb.top/posts/usage-of-gclone/)
