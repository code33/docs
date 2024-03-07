---
title: "将 jetbrains idea 的 keymaps 快捷键 导入至 vscode"
seoTitle: "import JetBrains IntelliJ IDEA Keymaps to Visual Studio Code"
seoDescription: "将 jetbrains idea 的 keymaps 快捷键 导入至 vscode"
datePublished: Thu Mar 07 2024 17:59:24 GMT+0000 (Coordinated Universal Time)
cuid: clthj8f7a00020ajo4lsb92u3
slug: jetbrains-idea-keymaps-vscode
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1709834206638/bd03f2c9-bfc5-4634-8b3b-2d7de47505d1.jpeg
tags: jetbrains, vscode-cjevho8kk00bo1ss2lmqqjr51, jetbrains-keymaps, vscode-keymaps

---

1. 将 idea的快捷键导出 export settings -&gt; keymaps(schemes); zip文件将其解压出xml文件
    
2. 在vscode中安装插件 IntelliJ IDEA Keybindings
    
3. 打开控制台 command palette 左下角的齿轮图标点击选择 command palette进入它: `Import IntelliJ Keybindngs (XML)`
    
4. 选中刚才解压出来的keymaps配置xml文件,若解压出有多个文件,则选择体积大的那个即可
    
5. 操作过程中会让你选择自己的操作系统
    
6. 操作完成后,把Untitled-1.json的内容复制到keybindings.json中覆盖并保存即可
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1709833860926/4b45ef50-6e4a-49cf-8311-2d563ac721e2.png align="center")
    

重启你的vscode试试吧~ 祝好运!