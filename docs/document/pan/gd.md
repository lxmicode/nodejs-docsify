## 谷歌云盘
### 申请团盘


### gd-utils docker版本
- 准备域名或公网IP，确保https能正常使用   
- 准备gd-utils 中的config.js 文件，用于覆盖docker中的对应文件   
(可选，可以在docker中直接修改配置)
- 准备SA文件   
```bash
##安装环境
sudo yum install -y git python3 python3-pip 
#拉取AutoRclone项目并进入项目文件夹
git clone https://github.com/xyou365/AutoRclone && cd AutoRclone
## 列出所有项目
python3 gen_sa_accounts.py --list-projects
##启用服务
sudo python3 gen_sa_accounts.py --enable-services gcp项目名
##为Project生成SA
sudo python3 gen_sa_accounts.py --create-sas gcp项目名
##下载指定Project中 SA 的授权文件，稍等片刻 
sudo python3 gen_sa_accounts.py --download-keys gcp项目名
##提取json文件到email文件
cat accounts/*.json | grep "client_email" | awk '{print $2}'| tr -d ',"' | sed '0~100G' > email.txt
##linux 查看文件夹中的个数
sudo ls -l | grep "^-" | wc
```
- 安装docker环境参考 [docker安装](https://docs.docker.com/engine/install/centos/)
- 安装 gd-utils-docker 镜像
```bash
#安装镜像
docker pull gdtool/gd-utils-docker
#启动镜像 
## 23333端口: gd-utils机器人，这里为了方便直接80(教程套了CF）
## 4200端口: webshell ,密码:your_self_passsword
## 8080: 文件管理界面，默认不启动 启动方式 cd / && filebrowser &,账号密码:admin
sudo docker run --restart=always  -d \
 -e USERPWD="your_self_passsword" \
 -p 4200:4200 \
 -p 8080:80 \
 -p 80:23333 \
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

## docker-aria2-pro
- 暂不推荐使用，无下载进度，更换python-aria-mirror-bot版本
### aria2-pro安装
- 文件准备   
 rclone.conf: rclone配置文件（或启动后容器后修改）   
 script.conf: 针对rclone脚本，主要配置rclone使用盘和上传路径(可以从官方git复制文件修改)   
 tele-aria2-conf.json: tg电报机器人配置   
 ```json
 {
  "aria2-server": "ws://aria2:6800/jsonrpc",
  "aria2-key": "aria2password",
  "bot-key": "机器人token",
  "user-id": "tg使用这ID，可以通过发信息给自己机器人，调用接口https://api.telegram.org/bot[Tocken]/getUpdates 查询",
  "max-index": 10
}
```
- 拉取镜像
```bash
$ sudo docker pull p3terx/aria2-pro
```
### tg-aria2 安装

- 拉取镜像
```bash
$ sudo docker pull p3terx/tele-aria2
```

### 编写compose/启动

```yml
version: "3.8"
services:
  ## aria2 客户端
  aria2:
    image: p3terx/aria2-pro
    container_name: aria2
    restart: unless-stopped
    environment:
      PUID: root
      PGID: root
      RPC_SECRET: aria2password
      RPC_PORT: 6800
      LISTEN_PORT: 6888
      SPECIAL_MODE: rclone
#不需要对外
#    ports: 
#      - 6800:6800
#      - 6888:6888
    volumes:
      - "/root/myConfig/docker/aria2/config:/config"
      - "/root/myConfig/docker/aria2/downloads:/downloads"
    networks:
      - tg-net

  ## tg aria2机器人配置
  tg-aria2:
    image: p3terx/tele-aria2
    container_name: tg-aria2
    volumes:
      - "/root/myConfig/docker/aria2/tele-aria2-conf.json:/config.json"
    environment:
      SPECIAL_MODE: rclone
    networks:
      - tg-net

networks:
  ##自定义网络,如果没有请先建立
  tg-net:
  ##使用默认网络，桥接宿主机，实现网络
  default:
    external:
      name: bridge
```

### 覆盖配置启动

```bash
$ \cp rclone.conf /root/myConfig/docker/aria2/config
$ \cp script.conf /root/myConfig/docker/aria2/config
$ cp tele-aria2-conf.json /root/myConfig/docker/aria2
$ sudo docker-compose up -d
```


## python-aria-mirror-bot(见参考lzzy12) 
- Docker版本带进度条(官方github谷歌翻译加自己调整-滑稽)

### 克隆项目及配置文件
```bash
git clone https://github.com/lzzy12/python-aria-mirror-bot mirror-bot/
cd mirror-bot
```
### 安装python3

### 安装docker

### 设置配置文件
```bash
# 复制一份配置文件
cp config_sample.env config.env
# 删除config.env中的一段代码
_____REMOVE_THIS_LINE_____=True
```
- config.env参数说明   
BOT_TOKEN：您从@BotFather获得的电报机器人令牌   
GDRIVE_FOLDER_ID：上传到的Google Drive文件夹的文件夹ID。   
DOWNLOAD_DIR：下载文件应下载到的本地文件夹的路径   
DOWNLOAD_STATUS_UPDATE_INTERVAL：一小段时间间隔（以秒为单位），之后更新镜像进度消息。（我建议至少保持5秒）   
OWNER_ID：机器人所有者的电报用户ID（而非用户名）   
AUTO_DELETE_MESSAGE_DURATION：多少秒后删除信息：设置为-1绝不会自动删除邮件   
IS_TEAM_DRIVE：（可选）是否团盘，则设置为“ True”，否则为False或保留为空。   
USE_SERVICE_ACCOUNTS：（可选）是否使用SA帐户，填True/False无知留空，如果填true,在根目录创建accounts然后放入SA json文件。   
INDEX_URL：（可选），请参阅https://github.com/maple3142/GDIndex/。URL不应包含任何结尾的“ /”   
TELEGRAM_API：（留空）从 https://my.telegram.org上获得。创建API development tools后获取。   
TELEGRAM_HASH： （留空）获取方式同TELEGRAM_API
USER_SESSION_STRING：通过运行下面代码生成的会话字符串：   
```bash
# 执行之后，需要输入上面获取的 TELEGRAM_API、TELEGRAM_HASH、加手机号
python3 generate_string_session.py

#此处可能会遇到一个错误 未找到telegraph模块，或者提示少什么安装什么
pip3 install telegraph
```
注意：您可以通过更改aria.sh中的MAX_CONCURRENT_DOWNLOADS值来限制最大并发下载。默认情况下，它设置为2   

### 获取Google OAuth API凭据文件

- 访问[Google Cloud Console](https://console.developers.google.com/apis/credentials)  
- 转到“ OAuth同意”标签，填写并保存。
- 转到“凭据”选项卡，然后单击“创建凭据”->“ OAuth客户端ID”
- 选择其他并创建。
- 使用下载按钮下载您的凭据。
- 将该文件移至mirror-bot的根目录，并将其重命名为凭据。
- 访问Google API页面
- 搜索驱动器并启用它（如果已禁用）
- 最后，运行脚本为Google云端硬盘生成令牌文件（token.pickle）：
```bash
pip install google-api-python-client google-auth-httplib2 google-auth-oauthlib
python3 generate_drive_token.py
```

### 编译镜像
- 构建Docker映像：
```bash
sudo docker build . -t mirror-bot
```
- 运行图像：
```bash
sudo docker run --name mirror-bot -itd mirror-bot
```

### 测试
- 建议第一次运行镜像，不要后台启动，方便查看日志，排除错误
```bash
sudo docker run --name mirror-bot mirror-bot
```
- 完成无错误之后再后台启动
```bash
sudo docker run --name mirror-bot -itd mirror-bot
```
- 测试下载上传，打开tg机器人输入 /help 根据命令操作即可

## 参考
> [gd-utils](https://github.com/iwestlin/gd-utils)  
> [gd-utils-docker](https://github.com/gdtool/gd-utils-docker)  
> [搬山之术](https://tech.he-sb.top/posts/usage-of-gclone/)  
> [AutoRclone](https://tech.he-sb.top/posts/usage-of-gclone/)  
> [blog.rneko.com](https://blog.rneko.com/archives/27/)  
> [p3terx](https://p3terx.com/archives/docker-aria2-pro.html)  
> [lzzy12](https://github.com/lzzy12/python-aria-mirror-bot/network/members)  
## 联系和讨论
- TG讨论群 https://t.me/gd_utils 看gd_utils ID没人使用，借用一下，`并不是官方群`，如果gd_utils原大佬想使用，可以联系。
