# OpenWrt-on-VMware
OpenWrt 本身获得自动翻墙能力，所有连入该路由的设备都可无障碍访问被墙的站点。

国内 IP 不代理，国外 IP 走代理。代理通过 Shadowsocks 所带 ss-redir 做 TCP 转发实现，国内 IP 通过 chnroute 来区别.

## VMware 
网络适配器 对应 eth0 内网接口 桥接模式

网络适配器2 对应 eth1 外网接口 桥接模式 `如果 VMware 宿主机上网需要配置，请改成 NAT 模式，或者自行配置 OpenWrt WAN 接口配置`

其他虚拟化软件类似 `esxi hyper-v virtualbox kvm`，创建虚拟机时添加两张网卡，都为桥接模式

## OpenWrt
- 未开启DHCP，以防扰乱现有局域网。如有需要请自行开启。
- 开启虚拟机，配置上网设备 IP 如下示例。[下载链接](https://github.com/luoqeng/OpenWrt-on-VMware/releases)

    IP:192.168.127.X

    Gateway:192.168.127.1

    DNS:192.168.127.1

- 浏览器输入 192.168.127.1 可以访问 LuCI 自行添加 Shadowsocks 服务器

    User:root

    Passwd:passwd@#2048

## 其他
推测墙现在的策略是未知加密流量随机断流几分钟，超级难受。相当于开启白名单模式了

当前各种自发明加密协议基本残废，跑在 [TLS](https://github.com/shadowsocks/v2ray-plugin) 协议里面比较稳妥，推荐搭配。

### 参考
 - https://cokebar.info/archives/664
 - https://github.com/haoel/haoel.github.io
 - https://github.com/luoqeng/OpenWrt-on-Docker

