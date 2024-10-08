## esxi + ros(拨号) + lede + newifi 2d AP

### 开启ssh
- ip - services 下面看到ssh都是有启动以及对应端口
### ros 查看公网IP
- interface-ppoe拨号客户端（pppoe-out1) - Local Address

### ros Nat转发(用于开公网转内网IP)
- ip - firewall- NAT - Add New - comment（字段重要,脚本用到）

| Chain | Dst. Address | Protocol | Dst. Port | Action | ActionTo Addresses | To Ports |
|  ----  | ----  | ----  | ----  | ----  | ----  | ----  |
| dstnat | 外网地址 | tcp | 对外端口 | 作用 | 内网服务器地址 |端口 |
| dstnat | 1.1.1.1 | tcp | 80 | dst-nat | 192.168.1.1 |80 |

### ros 转发 ip
- ip - firewall - Address Lists - Add New 添加一条记录
- ip - firewall - NAT - Advanced - Src. Address List - 选择自己新增的

### ros动态域名解析(动态DDNS 这里用了阿里云) 
- 创建Nat转发，参考上面
- 创建脚本

1. 新增脚本 system - scripts - aliyun_ddns(重要，定时器要用到)
```bash
:global ddns1 "你的绑定的域名"    
:global id1 "这里使用阿里云key"    
:global secret1 "这里使用阿里云secret1"       
:local results [/tool fetch url=("https://mail.ros6.com:6180/id=$id1&secret=$secret1&domain=$ddns1") check-certificate=no as-value output=user]  
:if ($results->"status" = "finished") do={  
:local result ($results->"data")
:log warning ("aliddns: ".$result)
} 

```
3. system - scheduler - Add New - 添加一个名为 aliyun_job 定时器 
4. On Event中填入 :execute aliyun_ddns (第一步的脚本名)
5. 下面关键字段（半小时执行一次阿里云DDNS,多个脚本同时执行在On Event另起一行）

| Start Time | Interval | On Event |
|  ----  | ----  | ----  |
| startup | 00:30:00 | :execute aliyun_ddns|
|   |   | :execute cf_ddns|

### ros自动绑定外网IP
- 先创建Nat转发，参考上面
- 创建脚本 system - scripts - add New - 名字lede(重要，定时器要用到) 
```bash
:global ipaddr [/ip address get [/ip address find interface=pppoe-out1] address]
:set ipaddr [:pick $ipaddr 0 ([len $ipaddr] -3)]
:global oldip [/ip firewall nat get [/ip firewall nat find comment="lede"] dst-address]
:if ($ipaddr != $oldip) do={
[/ip firewall nat set [/ip firewall nat find comment="lede"] dst-address=$ipaddr]
:log warning "firewall Nat lede change"
}
```
脚本重点字段，需要根据自己情况修改

| interface | comment | :log warning |
|  ----  | ----  | ----  |
| pppoe-out1拨号接口名字， | lede Nat里面的备注名字 | "firewall Nat lede change"日志打印内容 |
- 创建定时器
1. system - scheduler - Add New - 添加一个名为 lede_job 定时器 
2. On Event中填入 :execute lede (第一步的脚本名)
3. 下面关键字段（半小时执行一次，阿里云DDNS,多个脚本另起一行）

| Start Time | Interval | On Event |
|  ----  | ----  | ----  |
| startup | 00:30:00 | :execute lede |

### ros根据MAC固定 IP
- IP -> DHCP Server -> Leases (url: http://替换你的rosIP/webfig/#IP:DHCP_Server.Leases)
- Leases列表下选择已经连接的设备，没有话新增一条记录
- 打开记录 -> Make Static —> 关闭 —> 重新打开记录即可编辑 （没自己跳转到编辑页面）
