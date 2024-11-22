---
title: "docker下的postgresql备份shell脚本 1.0"
seoTitle: "docker postgresql postgres backup database data"
datePublished: Sun Jul 07 2024 19:46:12 GMT+0000 (Coordinated Universal Time)
cuid: clybyroui000209mc57qt7ih1
slug: dockerpostgresqlshell
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1720381445712/c99acc41-daca-417e-9a04-7fc6819ca60e.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1720381533974/be1c4dfd-7090-48a9-898a-2133a73c0a59.png
tags: postgresql, docker, backup

---

此为docker内postgres DB单库的全量备份操作

注意其中scp 的操作为,将备份出来的文件,传输到局域网内主机的某个目录

且不能有docker 命令包含 -it的参数,否则crontab 无法生效

```bash
#!/usr/bin/env bash

CONT_NAME="shm-pg"
DB_NAME="grpchub"
FILE_NAME="shm"
export PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:${PATH}"
export HOME="/home/app1"
# 备份ald数据库在容器内
/usr/bin/docker exec $CONT_NAME su - postgres -c "pg_dump -U postgres -F c -b -v ${DB_NAME} | xz -9e > /var/lib/postgresql/bak_${FILE_NAME}_$(date +%Y%m%d).xz"

# 列出ald数据库备份文件名,这里之前包含了 -it 是错误的写法
#file1=$(docker exec -it $CONT_NAME su - postgres -c 'ls -t /var/lib/postgresql | head -n 1')

output=$(/usr/bin/docker exec $CONT_NAME su - postgres -c "ls -t /var/lib/postgresql/*.xz | head -n 1")
# 检查并过滤掉不需要的信息
if [[ $output == *"/var/lib/postgresql/"* ]]; then
  # 使用字符串截取获取文件路径
   file_path=${output#*"/var/lib/postgresql/"}
   file_path="/var/lib/postgresql/$file_path"
   cmd="/usr/bin/docker cp $CONT_NAME:/var/lib/postgresql/bak_${FILE_NAME}_$(date +%Y%m%d).xz ${HOME}/bak"
   eval "$cmd"
   #/usr/bin/scp ${HOME}/bak/bak_${FILE_NAME}_$(date +%Y%m%d).xz n1a-root:/data/private-writable
   /usr/bin/curl -v -u user1:pass1 -T ${HOME}/bak/bak_${FILE_NAME}_$(date +%Y%m%d).xz "http://10.0.0.81:13880/private-writable/"
else
  echo "No .xz files found in the directory."
  exit 1
fi


# 打印文件路径
echo "$file_path"
```