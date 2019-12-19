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

  目前使用的 gost，gost 之间的使用的 h2 协议通信，gost 客户端开启 ss 服务适配 ss 客户端，ss 客户端比较完善。

  websocket 应该也可以，不过相比较 h2，多了一次升级握手流程。

  可用这个脚本 [Ubuntu 18.04 Installation Script ](https://github.com/haoel/haoel.github.io/blob/master/scripts/install.ubuntu.18.04.sh)

  [gost 设置 H2 服务](https://github.com/haoel/haoel.github.io#33-%E7%94%A8-gost-%E8%AE%BE%E7%BD%AE-https-%E6%9C%8D%E5%8A%A1) `国外VPS运行`
  ```
    ## 下面的四个参数需要改成你的
    DOMAIN="www.xxxx.com"
    USER="user"
    PASS="passwd"
    PORT=443

    BIND_IP=0.0.0.0
    CERT_DIR=/etc/letsencrypt
    CERT=${CERT_DIR}/live/${DOMAIN}/fullchain.pem
    KEY=${CERT_DIR}/live/${DOMAIN}/privkey.pem

    WEB_DIR=${HOME}/.www
    FILE_NAME=hello.txt
    mkdir -p ${WEB_DIR} && echo 'hello world' > ${WEB_DIR}/${FILE_NAME} # hello world 可改成其他字符，伪装成一个网站

    sudo docker run -d --name gost \
      -v ${CERT_DIR}:${CERT_DIR}:ro \
      -v ${WEB_DIR}:/var/www:ro \
      --net=host ginuerzh/gost \
      -L "http2://${USER}:${PASS}@${BIND_IP}:${PORT}?cert=${CERT}&key=${KEY}&probe_resist=file:/var/www/${FILE_NAME}"
  ```
  [gost 客户端开启 SS 服务](https://github.com/haoel/haoel.github.io#41-gost-%E5%AE%A2%E6%88%B7%E7%AB%AF) `推荐在局域网内运行或者国内VPS`
  ```
    ./gost -L ss2://aes-256-gcm:password@:1025 -F https://user:passwd@www.xxxx.com:443
  ```

### 参考
 - https://cokebar.info/archives/664
 - https://github.com/haoel/haoel.github.io
 - https://github.com/luoqeng/OpenWrt-on-Docker

