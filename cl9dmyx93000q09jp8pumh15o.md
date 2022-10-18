# scala sbt 项目编译卡住 或者 过慢的处理办法

经常发生一些新同学入职后,把公司的源码pull下来后 sbt 项目编译失败的情况;
这里提供一个最简单的办法:
  1. 确认老职员电脑上项目运行稳定的系统内找到 ~/.ivy2/cache
  2. 把老员工的~/.ivy2/cache 目录进行打包,如 tar -jcvf ivy2cache.tar.bz2 ~/.ivy2/cache
  3. 将 ivy2cache.tar.bz2 传至共享空间 or 发送至新伙伴安装到他自己电脑上根目录即可

确认 新同学本地的 ~/.ivy2/cache 下的类库 与 期望的相符

![libs-ivy2.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1666062782629/iHDwU7bW4.png align="left")
