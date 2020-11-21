## ros(拨号) + lede + newifi 2d(AP)


### ros 查看公网IP
- interface-ppoe拨号客户端（pppoe-out1) - Local Address

### ros 防火墙转发(打开公网转发)
- ip - firewall- NAT - Add New

| Chain | Dst. Address | Protocol | Dst. Port | ActionTo Addresses | To Ports |
|  ----  | ----  | ----  | ----  | ----  | ----  |
| dstnat | 外网地址 | tcp | 对外端口 | 内网服务器地址 |端口 |
| dstnat | 1.1.1.1 | tcp | 80 | 192.168.1.1 |80 |
