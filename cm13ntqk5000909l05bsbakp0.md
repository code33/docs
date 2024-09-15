---
title: "获取当前的外网ip地址命令"
seoTitle: "get my ip of wan"
seoDescription: "get my ip of wan"
datePublished: Sun Sep 15 2024 14:16:49 GMT+0000 (Coordinated Universal Time)
cuid: cm13ntqk5000909l05bsbakp0
slug: myip
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1726409708818/94a43fbd-698b-400b-a1be-53e7bf8a7a24.png
tags: myip

---

先进入你的 ~/.bashrc 或者 ~/.zshrc

贴入这个命令:

```plaintext
alias myip="curl -k 'http://ip-api.com/'"
```

然后将其生效即可

```plaintext
. ~/.bashrc
// 或者 . ~/.zshrc
```

然后输入myip 试试吧

```plaintext
myip
```