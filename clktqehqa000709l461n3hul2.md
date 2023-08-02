---
title: "nodejs 基于mocha工具单元测试配置,with coffeescript"
seoTitle: "nodejs testing coffee"
datePublished: Wed Aug 02 2023 12:55:58 GMT+0000 (Coordinated Universal Time)
cuid: clktqehqa000709l461n3hul2
slug: nodejs-mochawith-coffeescript
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1690979408882/edee3609-71e3-48cc-b0eb-fc50d19da42a.jpeg
tags: nodejs, coffeescript, testing

---

准备环境:

nodejs

idea

coffeescript

如图几个关键配置项文本在此贴上:

node options:

```bash
--compilers coffee:coffeescript/register
```

environment variables- 单元测试所需的环境变量:

moch package Mocha的应用所在,可以全局也可跟随项目,如图我这儿是跟项目的:

extra Mocha options-Mocha扩展配置,这里我们也给它加上 coffeescript的支持:

```bash
--compilers coffee:coffeescript/register
```

然后保存即可

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1690979546432/316c1a4c-a5de-4807-be44-50edc1c185be.jpeg align="center")

这里是调用的脚本和操作界面:

```bash
chai = require('chai')
should = chai.should()
chaiAsPromised = require('chai-as-promised')
chai.use(chaiAsPromised)
expect = chai.expect
log = require('captains-log')({level: 'silly'})

unless _ then _ = require('lodash')
unless process.env.NODE_ENV then process.env.NODE_ENV = 'local'
unless printDebug then printDebug = log.debug
unless printInfo then printInfo = log.info
unless printError then printError = log.error
process.env['TEST-UNIT']='1'
unless md
   md = {}
   md['async'] = require('asyncawait/async');
   md['await'] = require('asyncawait/await');

BeTestService = require('../../../api/services/utils')

context 'utils-->',->
   

   it 'action-Hello', ->
      (md.async ->
         try
            param = {}
            printDebug 'Hello'
            result='Hello'
#            result = md.await BeTestService.Hello(param, token)
            printDebug 'result-Hello', result
         catch err
            expect(err).to.equals(null)

      )()
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1690980324401/7f703e71-edca-4060-93f3-4d87de3ee9ed.png align="center")

如图,点击 it 左侧绿色的小图标,即可运行指定的单元测试了

当然也可以自己写脚本跑在shell命令行下:

```bash
node /mnt/e/project/grpchub/nd-subscribe/node_modules/mocha/bin/mocha --compilers coffee:coffeescript/register /mnt/e/project/grpchub/nd-subscribe/api/test-unit/services-main/tLodash.test.coffee --grep action-Hello
```

执行结果如下

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1690980820840/28fdbeca-f181-4e32-9c54-aa7e26d37c8e.png align="center")

end