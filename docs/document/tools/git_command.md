## 常用命令总结

### `clone`
- 克隆检出仓库
```bash
$ sudo git clone https://github.com/fzsimlu/nodejs-docsify.git
```

### `pull`
- 拉取代码&__合并代码__
```bash
$ sudo git pull
```

### `fetch`
- 拉取代码__不合并代码__
```bash
$ sudo git fetch
```

### `add`
- 添加修改文件
```bash
$ sudo git add 文件名
##提交加当前子目录下所有更改过的文件
$ sudo git add .
```

### `push`
- 提交代码
```bash
$ sudo git push
##强制提交 master 主分支
$ sudo git push -u origin +master
```

### `branch`
- 分支(branch)操作相关命令
```bash
##查看本地分支
$ sudo git branch
##查看远程分支
$ sudo git branch -r
```
- 创建分支
__注意新分支创建后不会自动切换为当前分支__
```bash
$ sudo git branch [name] 
```

- 切换分支
```bash
$ sudo git checkout [name]
```

- 创建新分支并立即切换到新分支
```bash
$ sudo git checkout -b [name]
```

- 设置默认编辑器
```bash
$ git config --global core.editor vim
#配置编辑
git config --edit
#全局配置编辑
git config --global --edit
```

- 存储密码
```bash
 git config credential.helper store
 git config --global user.name icode
 git config --global user.email icode@gmail.com
```

### 代码迁移
- 迁移思路：添加新仓库地址 -> 把老仓库地址提交到的仓库
- 合并思路：新仓库B -> 添加老仓库A地址 -> 合并B到A
```bash

# 1. 在B仓库的test项目中添加A仓库的test项目为远程仓库
git remote add A http://test.com/test.git

# 2. 获取A仓库的test项目master分支
git fetch A master

# 3. 合并A仓库的test项目master分支到B仓库的test项目master分支
git merge A/master

# 4. 推送更新到B仓库
git push origin master
```
- 一键脚本待完成
  
``` bash
#!/bin/bash

# 定义变量
SOURCE_REPO_URL="http://test.com/test-mgt"  # 源仓库的Git URL
TARGET_REPO_URL="git@example.com:user/target_repo.git"  # 目标仓库的Git URL
PROJECT_NAME="test-mgt"                              # 项目文件夹名称
BRANCH_PREFIX="origin/"                                 # 分支前缀，通常是"origin/"

# 克隆源仓库
echo "正在克隆源仓库..."
git clone $SOURCE_REPO_URL $PROJECT_NAME
cd $PROJECT_NAME

# 获取所有远程分支并创建本地分支
echo "获取远程分支列表..."
git fetch origin

# 创建所有本地分支并checkout
echo "创建并切换到所有远程分支..."
for remote_branch in $(git branch -r | grep -v '\->' | sed "s#$BRANCH_PREFIX##"); do 
    git checkout -b $remote_branch $BRANCH_PREFIX$remote_branch
done


# 初始化目标仓库
cd ..
echo "初始化目标仓库..."
git init target_project
cd target_project

# 配置远程目标仓库
echo "配置远程目标仓库..."
git remote add origin $TARGET_REPO_URL

# 确保我们在一个分支上（比如main/master），以便可以推送其他分支
echo "切换到主分支..."
git checkout -b main  # 或者使用master，取决于你的主分支名称

# 推送所有分支到目标仓库
cd ../$PROJECT_NAME
echo "开始推送所有分支到目标仓库..."
for branch in $(git branch | cut -c 3-); do
    echo "正在推送分支 $branch"
    git push -u $TARGET_REPO_URL $branch:$branch
done

echo "所有分支已推送至目标仓库。"

```

### 代理
```bash
# 设置全部代理
git config --global https.proxy http://127.0.0.1:7890
git config --global https.proxy https://127.0.0.1:7890

# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy

# 指定网址
git config --global http.https://github.com.proxy socks5://127.0.0.1:7890
```

## `练习`
- 例子
```bash
##clone 克隆代码
$ sudo git clone https://github.com/fzsimlu/nodejs-docsify.git
##新增/编辑文件
$ sudo vi test.txt
$ sudo git add test.txt
##提交提交备注
$ sudo git commit -m "备注"
##提交远程服务器
$ sudo git push
```

- 备注并提交
```bash
git commit -o dy_src -m 'auto' && git push
```

## 附录
- [git命令大全](https://gist.github.com/guweigang/9848271)
