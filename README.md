# 文档服务

> 使用`Docker`+`Docsify`搭建文档服务

## Docker环境配置

- 安装`Docker`，Link:[Centos7上安装配置Docker](https://docs.docker.com/engine/install/centos/)

- 安装`Docker Compose`，Link:[Docker Compose使用](https://www.runoob.com/docker/docker-compose.html)

- 编写`DockerFile.onbuild`文件，并使用该文件构建出基础镜像供后续使用

  ```shell
  FROM node:lts-stretch-slim
  
  RUN npm i docsify-cli -g --registry=https://registry.npm.taobao.org
  
  ONBUILD COPY docs /srv/docsify/docs
  ONBUILD WORKDIR /srv/docsify
  
  CMD ["/usr/local/bin/docsify", "serve", "docs"]
  ```

  ```shell
  $ docker build -t docsify:onbuild -f Dockerfile.onbuild .
  ```

## Docsify项目准备

项目结构说明:

```reStructuredText
项目跟路径
 ┣ docs
 ┃ ┃  ┣_sidebar.md  # 文档左侧导航
 ┃ ┣ _coverpage.md  # 文档首页导航
 ┃ ┗ index.html     # 文档右上角导航
 ┣ docker-compose.yml
 ┣ Dockerfile
 ┗ Dockerfile.onbuild
```

- `docker-compose.yml`

  > 用于在`Gitlab-Runner`上启动文档容器的脚本，内容如下

  ```yml
  version: '3.7'
  
  services:
    nodejs-docsify:
      image: my-docsify
      build:
        context: .
      container_name: nodejs-docsify
      hostname: docsify
      ports:
        - 80:3000
      volumes:
        - ./docs:/srv/docsify/docs
      #restart: always
      environment:
        - TZ=Asia/Shanghai
  ```

- `DockerFile`

  > 用于docker-compose构建容器进行的脚本，内容固定如下

  ```shell
  FROM docsify:onbuild
  ```

  其中`docker-compose 通过 docker-compose.yml + DockerFile 构建镜像 `
  
  执行一次以下命令完整部署
  ```shell
  $ docker-compose up -d
  ```



## 文档服务效果图

![文档服务效果图1](https://raw.githubusercontent.com/RobertoHuang/RGP-LEARNING/master/Others/images/%E6%96%87%E6%A1%A3%E6%9C%8D%E5%8A%A1%E6%95%88%E6%9E%9C%E5%9B%BE1.png)

![文档服务效果图2](https://raw.githubusercontent.com/RobertoHuang/RGP-LEARNING/master/Others/images/%E6%96%87%E6%A1%A3%E6%9C%8D%E5%8A%A1%E6%95%88%E6%9E%9C%E5%9B%BE2.png)

## 附录

- 参考文档:
  - [.gitlab-ci.yml语法详解 https://docs.gitlab.com/ee/ci/yaml/](https://docs.gitlab.com/ee/ci/yaml/)
  - [docsify文档生成器 https://docsify.js.org/#/?id=docsify-494](https://docsify.js.org/#/?id=docsify-494)
  - [VuePress静态网站生成器 https://vuepress.vuejs.org/zh/guide/](https://vuepress.vuejs.org/zh/guide/)

