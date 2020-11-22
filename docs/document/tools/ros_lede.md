## ros(拨号) + lede + newifi 2d(AP)

### 开启shh
- ip - services 下面看到ssh都是有启动以及对应端口
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
2. 新增脚本 system - scripts - 重点名字
```bash
:global ipaddr [/ip address get [/ip address find interface=pppoe-out1] address]
:set ipaddr [:pick $ipaddr 0 ([len $ipaddr] -3)]
:global oldip [/ip firewall nat get [/ip firewall nat find comment="5000"] dst-address]
:if ($ipaddr != $oldip) do={
:log info [/ip firewall nat set [/ip firewall nat find comment="5000"] dst-address=$ipaddr]
}
```
3. system - scheduler - Add New 添加一个定时器 
4. On Event中填入 :execute + 脚本名
