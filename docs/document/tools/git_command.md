## 常用命令总结
### `pull` 
- 拉取代码&__合并代码__
```git
$ sudo git pull
```

### `fetch` 
- 拉取代码__不合并代码__
```git
$ sudo git fetch
```

### `add` 
- 拉取代码__不合并代码__
```git
$ sudo git add 文件名
```

### `push` 
- 提交代码
```git
$ sudo git push
##强制提交 master 主分支
$ sudo git push -u origin +master
```

### `练习`
- 例子
```git
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
