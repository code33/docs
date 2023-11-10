---
title: "Ubuntu Linux 下将远程服务器备份打包的文件夹传至本地"
seoTitle: "auto backup crontab scripts"
datePublished: Wed Oct 11 2023 06:34:00 GMT+0000 (Coordinated Universal Time)
cuid: clnldkwqr000309mkfg8y6nns
slug: ubuntu-linux
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1699640484494/e6ac86ee-a0ad-4c13-83c3-75be6c915827.jpeg
tags: backup, database-backup

---

这是远端对数据库文件的打包备份

```bash
#!/bin/bash

tar -jcvf "/home/appsrv/bak/dc-pg0-shenmei-$(date +'%y%m%d%H%M').tar.bz2" /home/appsrv/docker-cp/dc-pg0
```

这个是从远程传至本地的script shell 脚本

```bash
#!/bin/bash

# 远程主机信息
remote_host="remoteHostname"
remote_dir="/remotedata/bak"

# 本地存储目录
local_dir="/home/localdata/dbbak"
prefix_file="dc_s" # 这里是文件前缀可自行变更

# 使用SSH和find命令在远程主机上查找最新的tar.bz2文件
latest_backup=$(ssh "$remote_host" "find '$remote_dir' -name '${prefix_file}*.tar.bz2' -type f -exec stat -c '%Y %n' {} + | sort -n | tail -n 1 | awk '{print \$2}'")

# 检查是否找到文件
if [ -z "$latest_backup" ]; then
  echo "在远程主机 $remote_host 中未找到任何tar.bz2文件在目录 $remote_dir 中"
  exit 1
fi

echo "远程主机 $remote_host 中最新的tar.bz2文件是: $latest_backup"

# 使用rsync同步文件到本地目录
rsync -avz -e "ssh" "$remote_host:$latest_backup" "$local_dir/"

echo "已将文件同步到本地目录: $local_dir"
```