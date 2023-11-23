---
title: "让grpc项目支持postman or grpcurl 接口调试"
datePublished: Thu Nov 23 2023 00:34:04 GMT+0000 (Coordinated Universal Time)
cuid: clpagno0g000109l59hd4bhls
slug: grpcpostman-or-grpcurl
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1700699542648/a8025bec-d789-478b-9a9d-cec20c2d86e0.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1700699605661/729ba8c4-fc47-473b-a17c-16dede2e2353.png
tags: grpcurl, grpc-postman, grpc-test

---

dotnet已然成了最强的gprc server开发环境,就因为脚手架 以及开发配套的便捷性

这里添加1个program.cs的配置即可实现postman 和 grpcurl命令行工具的接口调试

```bash

builder.Services.AddGrpc();
builder.Services.AddGrpcReflection();
var app = builder.Build();
IWebHostEnvironment env = app.Environment;

if (env.IsDevelopment())
{
   //开启此项配置后,可在postman 或 grpcurl cmd中直接调用rpc接口,此为示例
   //grpcurl -d '{"name":"YOyo"}' -plaintext 127.0.0.1:50873 greet.Greeter.SayHello
   app.MapGrpcReflectionService();
}
```