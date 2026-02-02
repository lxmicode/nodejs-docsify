## python

### 1. 基础依赖安装
别指望白手起家，先把编译 Python 必须的工具链装全。

```bash
# 更新 brew
brew update

# 安装编译 Python 所需的底层依赖，缺这些你会编译报错到怀疑人生
brew install openssl readline sqlite3 xz zlib tcl-tk
```

### 2. 安装 pyenv
别废话，直接用 Homebrew。

```bash
brew install pyenv
```

### 3. 配置 Shell 环境变量 (关键步骤)
别写在 `~/.bash_profile` 里了，现在 macOS 默认是 `zsh`。如果你这步配错了，`pyenv` 就是个摆设。

```bash
# 将以下配置写入 ~/.zshrc
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo '[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc

# 立即生效
source ~/.zshrc
```

### 4. 常用操作指令
只会装不会用？看好了。

```bash
# 查看当前可安装的版本 (太多了，建议加 grep 过滤)
pyenv install --list | grep "3.10"

# 安装指定版本 (别用太老的，建议 3.10.x 或 3.11.x)
pyenv install 3.10.13

# 设置全局 Python 版本 (没事别乱动系统 Python)
pyenv global 3.10.13

# 针对当前项目目录设置特定版本 (这才是架构师该做的)
pyenv local 3.11.5

# 查看当前生效的版本
pyenv version
```


### 服务器版本切换
- 当服务存在多版本，默认一个，修改/etc/profile
```bash
alias python='/usr/bin/python3'
```

### 推导式
- 可以从一个数据序列构建另一个新的数据序列的结构体。
- 只有列表[]、字典{key:val}、集合{} 能使用推导式


- 列表推导式-for改成推导式如下
```bash
list=[]
for i in range(10):
  list.append(i)
print(list)
## for改成推导式
list = [i for i in range(10)]
print(list)
```

- 字典推导式
```bash
dict = {i:i**2 for i in range(10)}
print(dict) #{1: 1, 2: 4, 3: 9, 4: 16}
##合并字段数据
list1 = ['id','name']
list2 = ['001','jack']
dict = {list1[i]:list2[i] for i in len(list1)}
print(dict) # {'id': '001', 'name': 'jack'}
## 提取字典数据
dict = {'DELL':201,'acere':99}
count = {key:val for key,val in dict.items() if val>200}
print (count) # {'DELL':201}
```

- 集合推导式
```bash
set1 = {i for i in range(10) if i%2==0 }
print(set1) #{2,4,6,8}
```

### sublime集成
- 添加配置
```json
{
	"cmd": ["D:/tools/python3/python.exe","-u","$file"],
	"file_regex":"^[ ]*File \"(...*?)\", line ([0-9]*)",
	"selector":"source.python",
	"encoding": "utf-8" ,
	"env": {"PYTHONIOENCODING": "utf8"}
}
```

