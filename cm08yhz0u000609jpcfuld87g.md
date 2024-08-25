---
title: "中国地区的oh-my-zsh 安装"
seoTitle: "国内安装ohmyzsh"
seoDescription: "国内安装ohmyzsh"
datePublished: Sun Aug 25 2024 02:34:45 GMT+0000 (Coordinated Universal Time)
cuid: cm08yhz0u000609jpcfuld87g
slug: oh-my-zsh
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1724553214065/c1cb40a9-33cf-4da3-8185-f9643c62d3a6.jpeg
tags: zsh, ohmyzsh, ohmyzs

---

## 安装 oh-my-zsh

先将zsh安装到系统内然后.

```bash
sh -c "$(wget -O- https://gitee.com/pocmon/ohmyzsh/raw/master/tools/install.sh)"
```

## 挂载代理配置

```bash
export PROXY_HOST=127.0.0.1
export PROXY_PORT=17890
export HTTP_PROXY="http://$PROXY_HOST:${PROXY_PORT}"
export HTTPS_PROXY="http://$PROXY_HOST:${PROXY_PORT}"

alias myip="curl -k 'https://api.ipify.org?format=json'"
```

这里的 127.0.0.1 HOST 和 17890 PORT 是源自于 frp的配置转发出来的,具体不懂咋操作的可以留言并留下你的邮箱信息待我给你细细解答

## 安装 zoxide

执行完后 得 注意它的初始化脚本写入~/.zshrc 文件中

```bash
curl -k -sS https://webinstall.dev/zoxide | bash
eval "$(zoxide init zsh)"
```