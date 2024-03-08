---
title: "多线程压缩命令pbzip2,让bz2压缩起飞"
seoTitle: "multithreaded compression bz2"
seoDescription: "multithreading-compression multithreaded compression bz2"
datePublished: Fri Mar 08 2024 18:56:08 GMT+0000 (Coordinated Universal Time)
cuid: cltj0p8nw00010al9ednt69pr
slug: pbzip2bz2
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1709922862811/c30fe377-47a6-4459-acfa-d2fde959a8af.png
tags: compression, bz2, accelerate, multithreading-compression

---

随着nodejs的包越来越多 越多越臃肿,想要整个迁移打包,日常用的bz2都得超过1分钟压缩个 400多MB的文件夹,都是js小文件,以前用的古老 tar -jcf 慢得够打一盘斗地主了;

找到了 pbzip2这货就忒生猛了,支持单文件得 压缩和解压,配合tar即可实现文件夹的压缩和解压的 加速了

项目文件夹内是360多MB的碎片js小文件,先看看耗时:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709923704489/3ee2c7fe-0e85-4986-8706-3b8d98dc52b5.png align="center")

这里是压缩前后体积对比363M-&gt;49M 花了7秒,Intel N100的硬件,系统是Ubuntu 22:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709923753980/9ddef7c7-5b68-4311-ba9d-d7be2dd0ebfb.png align="center")

1. 安装pbzip2 root身份下 `apt-get update; apt-get install pbzip2`
    
2. 分别构建压缩和解压的脚本,於可执行目录内 `touch pbz2a.sh` 和 `touch pbz2x.sh`
    
3. 简单易用-压缩: `pbz2a.sh yourDirName` 解压 `pbz2x.sh yourDirName`
    
4. 分别贴入2者的脚本内容
    
    ```bash
    #!/bin/bash
    # pbz2a.sh
    # 检查参数数量是否正确
    if [ "$#" -lt 1 ] || [ "$#" -gt 2 ]; then
        echo "Usage: $0 <folder_name> [archive_name_prefix]"
        exit 1
    fi
    start_time=$(date +%s%3N)
    # 接受参数
    folder_name="$1"
    archive_name_prefix="${2:-$folder_name}"
    
    # 执行压缩命令
    tar -cf - "$folder_name" | pbzip2 > "${archive_name_prefix}.tar.bz2"
    
    end_time=$(date +%s%3N)
    
    duration=$((end_time - start_time))
    
    echo "Compression time: $duration milliseconds"  
    ```
    
    ```bash
    #!/bin/bash
    # pbz2x.sh
    # 检查参数数量是否正确
    if [ "$#" -ne 1 ]; then
        echo "Usage: $0 <compressed_file>"
        exit 1
    fi
    
    # 接受参数
    compressed_file="$1"
    start_time=$(date +%s%3N)
    
    # 解压缩压缩文件
    pbzip2 -df "$compressed_file"
    
    # 提取文件名和路径
    filename=$(basename "$compressed_file")
    extension="${filename##*.}"
    filename_noext="${filename%.*}"
    
    # 检查压缩文件是否以 .tar.bz2 结尾
    if [ "$extension" != "bz2" ]; then
        echo "Error: $compressed_file is not a valid .bz2 file"
        exit 1
    fi
    
    # 解包 tar 文件
    tar -xf "${filename_noext}"
    
    prefix=$(echo "$filename" | sed 's/\..*//')
    
    #prefix=$(basename "$extension" .tar.bz2)
    #echo "prefix-->$prefix"
    # 删除解压后的 tar 文件
    rm -f "${prefix}.tar"
    
    end_time=$(date +%s%3N)
    duration=$((end_time - start_time))
    echo "decompress time: $duration milliseconds"
    #duration=$((end_t
    ```