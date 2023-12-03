---
title: "启动dotnet server服务项目with envname 环境变量"
datePublished: Sun Dec 03 2023 18:05:19 GMT+0000 (Coordinated Universal Time)
cuid: clppsm3nb000009jo61fqb07f
slug: dotnet-serverwith-envname
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1701626657995/dae875a1-ff31-4466-8415-954908095159.jpeg
tags: dotnet-startup, startup-with-env

---

今天自己整了个很傻逼的事,就因为Production的配置文件项目木有贴完整,以至于docker 容器一直无法顺利地在env = prod的情况下顺利启动

于是把源码放到Linux下run了一下,加环境变量 在 Properties文件夹 果然一样的报错,找不到对应的配置项xxx啥的;

解决这个配置项启动环境变量忘记更改的沙雕行为,写一个用环境变量启动的startup脚本

```bash
#!/bin/bash

if [ $# -ne 0 ]; then
    # 第1个参数
    runEnv=$1
else
    # 默认环境变量
#    runEnv="Production"

   runEnv="Development"
fi


filePath="Properties/launchSettings.json"
json_content=$(cat "$filePath")
# □~N□□~O~V key 为 "ASPNETCORE_ENVIRONMENT" □~Z~D value □~@□
value1=$(echo $json_content | jq -r ".profiles[].environmentVariables.ASPNETCORE_ENVIRONMENT")

echo "ASPNETCORE_ENVIRONMENT file value is: $value1"
echo "expected variable to be: $runEnv"

if [ "$value1" != "$runEnv" ]; then
   sed -i "s/${value1}/${runEnv}/g" "$filePath" # run in Prod
fi
#sed -i 's/Production/Development/g' "$filePath" # run in Dev
jq -r '.profiles[].environmentVariables.ASPNETCORE_ENVIRONMENT' $filePath
envName1=$(echo "$json_content" | jq -r '.profiles[].environmentVariables.ASPNETCORE_ENVIRONMENT')
echo $envName1
#echo "ASPNETCORE_ENVIRONMENT □~@□为□□~Z$value"
dotnet run
```

保存到dotnet server的项目目录中

使用方法

```bash
./startup.sh 
```

或者

```bash
./startup.sh Production
```