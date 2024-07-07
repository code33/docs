---
title: "docker下的postgresql备份shell脚本"
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

```bash
#!/usr/bin/env bash

CONT_NAME="hdzpg-prod" # docker容器名
DB_NAME="victoria"     # postgresql-DB名
FILE_NAME="vicdb"      # 备份后的文件名
# 备份ald数据库在容器内
docker exec -it $CONT_NAME su - postgres -c "pg_dump -U postgres -F c -b -v ${DB_NAME} | gzip > /var/lib/postgresql/bak_${FILE_NAME}_$(date +%Y%m%d).gz"

output=$(docker exec -it $CONT_NAME su - postgres -c "ls -t /var/lib/postgresql/*.gz | head -n 1")
# 检查并过滤掉不需要的信息
if [[ $output == *"/var/lib/postgresql/"* ]]; then
  # 使用字符串截取获取文件路径
   file_path=${output#*"/var/lib/postgresql/"}
   file_path="/var/lib/postgresql/$file_path"
   cmd="docker cp $CONT_NAME:/var/lib/postgresql/bak_${FILE_NAME}_$(date +%Y%m%d).gz ${HOME}/bak"
   eval "$cmd"
   /usr/bin/scp ${HOME}/bak/bak_${FILE_NAME}_$(date +%Y%m%d).gz n1a-root:/data/private-writable
else
  echo "No .gz files found in the directory."
  exit 1
fi


# 打印文件路径
echo "$file_path"
    
```