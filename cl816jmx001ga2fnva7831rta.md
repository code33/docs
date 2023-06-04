---
title: "grpc statusCode最佳实践(个人理解)"
seoTitle: "grpc"
seoDescription: "grpc"
datePublished: Wed Sep 14 2022 05:26:04 GMT+0000 (Coordinated Universal Time)
cuid: cl816jmx001ga2fnva7831rta
slug: grpcstatuscode

---

create by jyo on 14/09/2022 concat: code0515@outlook.com

其中的各个解释都是本人自己从业数年的理解;若有更优欢迎补充

```sql

enum EmStatusCode {
   OK = 0; // 正常
   CANCELLED = 1; // 已撤销,用户某个操作发生了回滚以致于撤销的操作;场景如火车订票,你想要的车子被抢了...
   UNKNOWN = 2; // 未知异常
   INVALID_ARGUMENT = 3; // 非法参数,如手机号,邮箱地址格式不对,验证码不正确都可用此异常值
   DEADLINE_EXCEEDED = 4;// 请求超期,or同一资源请求过于频繁
   NOT_FOUND = 5; // 资源找不到,如对某一则信息进行更新or删除类的写操作,发生资源对象不存在的情况就报此异常
   ALREADY_EXISTS = 6; // 已经有了的重复请求,如新建or更新提交某一用户数据
   PERMISSION_DENIED = 7; // 权限不足以进行对此接口or资源的调用操作,如permission不足,or role 不匹配
   RESOURCE_EXHAUSTED = 8; // 配额资源已耗尽,如腾讯的微信token生成配额,就是有限制的,如果不做缓存其实就是会被耗尽报错
   FAILED_PRECONDITION = 9;// 前置条件失败or还未满足前置条件,无法执行当前操作;
   // 场景1: 如某流程业务中,还未提交审核(前置流程)就对其进行发货or退款的操作就可以抛此异常;
   // 场景2: 如对某客户信息进行销户处理,但其账户还存有未归档的订单则可抛此异常;
   // 但凡涉及到流程相关,其对象若进行删除or关闭之类操作,则都建议做FAILED_PRECONDITION的设计
   ABORTED = 10; // 中止执行,较长时长的一个请求操作如stream型返回(如视频文件上传下载断开,视频聊天断开)
   // 则,对其进行中止操作,则返回aborted
   OUT_OF_RANGE = 11; // 超出范围,如输入参数长度已经超出了边界限定,或者业务订单中的库存数量不足
   UNIMPLEMENTED = 12; // 未实现的异常,一般来自于框架的报错
   INTERNAL = 13; // 内部异常,一般捕获db异常,就用此code值,如表错误,字段错误,db函数错误等
   UNAVAILABLE = 14; // 某接口被替换or资源作废,一般说明中会提示建议使用其它的资源or接口
   DATA_LOSS = 15; // 数据丢失,对象是存在的但是其部分数据发生了丢失
   UNAUTHENTICATED = 16; // 未有登入
}
```