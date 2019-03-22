# OpenWrt-on-VMware
Shadowsocks + ChnRoute 实现智能透明代理

## 方案简介
- 基于 aa65535 luci-app-shadowsocks/openwrt-shadowsocks，在 OpenWRT/LEDE 下配置自动翻墙，支持在 LuCI 下图形化配置，大大简化了配置过程。配置完成后，路由器本身获得自动翻墙能力，所有连入该路由的设备都可无障碍访问被墙的站点。是运行于路由器的透明代理。
- 根据 IP 判断是否代理，国内 IP 不代理，国外 IP 走代理。代理通过 Shadowsocks 所带 ss-redir 做 TCP 转发实现，分国内外 IP 通过国内 IP 段列表文件 chnroute 来区别，并通过 iptables 规则分别处理。
- 由于是基于 IP 的判定，故需要解决 DNS 污染的问题，本方案使用优化的 DNS 解析方案，除过防 DNS 污染，同时具有良好的 CDN 兼容性，大部分情况下是最优的解析结果（缺点是配置过程稍繁琐）。
- 本方案进一步优化，增加 GFWList 和 Dnsmasq-China-List，使得 GFWList 中域名强制走代理，而 Dnsmasq-China-List 中的域名则强制直连，并且可以自行定义强制走代理或者不走代理的域名。
- 运行在VMware上，一般可直接打开使用。实现参考 [Shadowsocks + ChnRoute 实现 OpenWRT / LEDE 路由器自动翻墙](https://cokebar.info/archives/664)

## VMware 
- 网络适配器 对应 eth0 内网接口 桥接模式
- 网络适配器2 对应 eth1 外网接口 桥接模式 （如果 VMware 母机上网需要配置，请改成 NAT 模式，或者自行配置 OpenWrt WAN 接口配置）

## OpenWrt
- 未开启DHCP，以防扰乱现有局域网。如有需要请自行开启。
- 开启虚拟机，配置上网设备 IP 如下示例。

    IP:192.168.127.X

    Gateway:192.168.127.1

    DNS:192.168.127.1

- 浏览器输入 192.168.127.1 可以访问 LuCI

    User:root

    Passwd:passwd@#2048

## Docker
- Docker 运行 OpenWrt: https://github.com/luoqeng/OpenWrt-on-Docker

## OpenWrt 路由器推荐
- https://hobgear.com/best-openwrt-lede-routers/
- https://bluegadgettooth.com/best-openwrt-router/
