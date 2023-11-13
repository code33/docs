---
title: "Docker pull 中国大陆的镜像资源服务"
datePublished: Mon Nov 13 2023 02:14:52 GMT+0000 (Coordinated Universal Time)
cuid: clow9urzv000208jr58e675pz
slug: docker-pull
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1699841645190/ca8f3cae-142d-4c7a-a4f9-dc27ac8c9279.png
tags: docker-images, china-mirror, docker-mirro

---

1. 先进入到root权限
    
2. 直接对 /etc/docker/daemon.json进行覆盖写入
    
    ```bash
     echo '{                                                                                                 130 ↵
       "registry-mirrors": [
          "https://docker.nju.edu.cn/",
          "https://ung2thfc.mirror.aliyuncs.com",
          "https://registry.docker-cn.com",
          "http://hub-mirror.c.163.com",
          "https://docker.mirrors.ustc.edu.cn"
       ]
    }' >  /etc/docker/daemon.json
    ```
    

1. 然后重启docker的服务即可生效
    
    ```bash
    systemctl daemon-reload
    systemctl restart docker
    ```