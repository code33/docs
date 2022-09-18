## New project build by grpc and protobuf on startup

create by jyo on 18/09/2022
contact: code0515@gmail.com 

就目前而言,绝大部分(几乎所有)的项目都是从db 层 或者 前端 view 可视层入手; 而非从抽象层入手;作为项目的起点;为什么?
顾名思义,抽象层其逻辑期初,它是不可视,抽象的;至少在UML 设计稿出现之前;还不好客观地对项目进行开发评估(如用时/耗费)

当前地新项目立项方式:

- 业务流程 by diagram or Visio processOn 等
- 功能清单 by xmind 等
- 前端界面 by figma axure sketch等 
- 服务接口 by 需求讨论会,excel,dbTable... what? 黑人问号脸~

是不是发现问题了?
为什么 业务流程 功能构建 前端界面 都有标准化工具对其支持,而到了服务接口这个环节就没了呢?
其实早就有了的, 就是 protobuf 即 基于 protobuf的服务端设计方案构建 如 Grpc 和 [dubbo] (https://dubbo.apache.org/zh/blog/2019/10/28/dubbo-%E5%9C%A8%E8%B7%A8%E8%AF%AD%E8%A8%80%E5%92%8C%E5%8D%8F%E8%AE%AE%E7%A9%BF%E9%80%8F%E6%80%A7%E6%96%B9%E5%90%91%E4%B8%8A%E7%9A%84%E6%8E%A2%E7%B4%A2%E6%94%AF%E6%8C%81-http/2-grpc-%E5%92%8C-protobuf)

只不过大家对它们关注的少;

可使用 proto 文件 构建项目所需的Server端模块,及API method所关联的request struck and response struck 亦或 req res 的 stream struck 设计

由设计驱动项目的立项建设

再基于立项文件,设计文稿进行软件工程的预算工作开展

接下来的文章内容,将展开以todo example项目的project build startup by protobuf