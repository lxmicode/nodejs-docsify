## ros(拨号) + lede + newifi 2d(AP)


### ros 查看公网IP
- interface-ppoe拨号客户端（pppoe-out1) - Local Address

### ros Nat转发(用于开公网转发)
- ip - firewall- NAT - Add New

| Chain | Dst. Address | Protocol | Dst. Port | Action | ActionTo Addresses | To Ports |
|  ----  | ----  | ----  | ----  | ----  | ----  | ----  |
| dstnat | 外网地址 | tcp | 对外端口 | 作用 | 内网服务器地址 |端口 |
| dstnat | 1.1.1.1 | tcp | 80 | dst-nat | 192.168.1.1 |80 |

### ros 转发ip限制
- ip - firewall - Address Lists - Add New 添加一条数记录
- ip - firewall - NAT - Advanced - Src. Address List - 选择自己新增的

### ros动态IP映射端口 （参考：https://www.vediotalk.com/archives/2974）
1. 参考防火墙转发新增，重点comment字段名字
