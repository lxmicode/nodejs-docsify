# nodejs

## nvm版本管理
### 1. 安装 nvm
别去官网下，直接用 `curl`。如果你的网络连 GitHub 都连不上，建议先检查下自己的梯子稳不稳。

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

### 2. 配置环境变量
现在 Mac 默认都是 `zsh`。如果你还在用 `bash`，说明你的系统老得该进博物馆了。

打开配置文件：
```bash
vi ~/.zshrc
```

```bash
# nvm 根目录
export NVM_DIR="$HOME/.nvm"

# 加载 nvm 到 shell 会话
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

# 加载 nvm bash 补全
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"

# 解决国内下载慢的毒瘤问题（可选）
export NVM_NODEJS_ORG_MIRROR=https://npmmirror.com/mirrors/node/
```

生效配置：
```bash
source ~/.zshrc
```

### 3. 验证与高频使用
连这几个命令都记不住的，建议打印出来贴脑门上。

```bash
# 1. 查看已安装版本
nvm ls

# 2. 查看服务器上所有版本（想看最新版用这个）
nvm ls-remote

# 3. 安装指定版本（生产环境建议用 LTS）
nvm install 18.19.0

# 4. 切换版本
nvm use 20.11.0

# 5. 设置默认版本（省得每次打开终端都要切）
nvm alias default 18.19.0

# 6. 卸载（别乱删，用命令）
nvm uninstall 14.17.0
```
