# 麒麟系统

## 银河麒麟
### 系统信息
- ky10 sp1
- yum管理包
- aarch64


### nginx 安装
- linux一致的编译安装

### wkhtmltopdf
- [centos-aarch64-rpm](https://wkhtmltopdf.org/downloads.html)
- 安装环境
```bash
yum install -y xorg-x11-fonts-75dpi fontconfig
rpm -ivh xx.rpm
```
- 添加中文字体
```bash
cd /usr/share/fonts
# C:\Windows\Fonts，该文件夹下就存放相关字体，将simhei.tty拷贝到linux 目录/usr/share/fonts/my_fonts
# 清理缓存
fc-cache
#查看已安装的中文字体
fc-list :lang=zh-cn
```
