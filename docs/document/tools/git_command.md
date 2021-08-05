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
