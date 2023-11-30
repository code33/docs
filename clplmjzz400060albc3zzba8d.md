---
title: "Ubuntu的网卡设置,IP地址固定,DNS服务地址设置"
datePublished: Thu Nov 30 2023 20:04:38 GMT+0000 (Coordinated Universal Time)
cuid: clplmjzz400060albc3zzba8d
slug: ubuntuipdns
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1701374503435/08ecf678-f549-4353-8b32-b5190075a8a2.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1701374661967/f2bf6c42-9968-4ba9-8f12-46e8ce662fa8.jpeg
tags: ubuntu-dns, ubuntu-network-setting, ubuntu-netplan, ubuntu-network

---

一般Ubuntu的一只台式机,系统安装好了后,第一件事就是查询路由器内,是否有对网卡mac和内网IPv4地址的绑定

这里我的内网路由器网关ip地址是 10.0.0.1 ,其配置文件的路径是

`/etc/netplan/01-network-manager-all.yaml`

可能每位小伙伴的文件有些许不同,因为Ubuntu的版本号,or发行版本的差异,反正在 /etc/netplan里面都能找到此yaml文件即可

设置完成了后,别网卡网卡服务得要将配置执行生效:

`sudo netplan apply`

```bash
network:
   ethernets:
      enp2s0:
        #addresses: ['192.168.10.8/24']   # 配置的静态ip地址和掩码
         addresses: ['10.0.0.81/24']   # 本机局域网IP
         dhcp4: false   # 关闭dhcp4
         optional: true
         gateway4: '10.0.0.1' # 路由器/网关地址
         nameservers:
            addresses:
               - '223.5.5.5'
               - '209.146.104.69' # 这里查得可用DNS服务 public-dns.info/nameserver/hk.html
               - '119.29.29.29'
               - '8.8.4.4'
               - '211.140.13.188'
               - '114.114.114.114'  # DNS服务器地址，多个DNS服务器地址需要用英文逗号分隔开，可不配
  version: 2
  renderer: NetworkManager
```

其实呐,这个网卡设置中的操作是物理机设置的方案

还有个是VPS的方案即IP地址已经给分配好了,只需要手动配置DNS服务即可

打开 配置文件 `vim /etc/systemd/resolved.conf`

设置好了后记得充值网卡设置生效

`systemctl restart systemd-resolved.service`

```bash
[Resolve]
DNS=223.5.5.5
DNS=8.8.4.4
#FallbackDNS=
#Domains=
#LLMNR=no
#MulticastDNS=no
#DNSSEC=no
#Cache=yes
#DNSStubListener=yes
```

设置完成并重启网卡配置服务后,可以ping一下域名看看响应时间

更多合适的DNS服务地址可以自己去这个网站测试,

香港: `public-dns.info/nameserver/hk.html`

全球: `public-dns.info`