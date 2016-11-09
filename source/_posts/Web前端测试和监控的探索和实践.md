---
title: Web前端测试和监控的探索和实践
date: 2016-11-08 15:52:29
tags: [前端测试,前端监控]
---

## 引言
每天早上到公司后，我都会先翻阅RSS上订阅的技术文章，前几天在看文章的时候突然注意到这样一句话
> 程序开发三板斧：功能、测试和监控。

看到这里我停了一下，仔细琢磨，发现这句话总结的非常到位和精辟。作为程序员，可能大部分人关注的只是功能需求的开发，但并不关心程序的测试和监控方面的问题，往往容易被忽略的问题有时候却非常的重要。看完文章后我就开始着手研究前端测试和监控方面的东西。以下是我的调研总结：

## 前端测试
我所理解的前端测试主要分为单元测试、UI测试、性能测试。

单元测试主要是对方法和类库的测试，单元测试最长用的两个库是  [Mocha](https://mochajs.org/) 和 [Jasmine](https://jasmine.github.io/),通过写测试脚本来对小模块的代码进行测试，具体教程请参考 [测试框架 Mocha 实例教程 —— 阮一峰](http://www.ruanyifeng.com/blog/2015/12/a-mocha-tutorial-of-examples.html)。单元测试存在个弊端，就是你需要去写大量的脚本去跑测试用例，而且如果需求变动，你的代码逻辑就会变动，所以你的测试脚本也要做相应的维护。如果是在大公司，人力充足有专门测试人员的情况下，以上的问题应该不是问题，但是像大多数人所处的小公司，业务有时候可能都开发不完，更没时间维护测试脚本了。所以做单元测试需要消耗一定精力，量力而行。

UI测试方面包括对页面样式的测试，比如在各种浏览器下的显示效果有没有出现异常等等，还包括对交互的测试，比如点击按钮是否会有相应的反馈等等。性能测试主要是对网页加载速度的检测，包括不同网络环境不同设备的情况下加载情况的的测试，以上两个方面的测试推荐大名鼎鼎的测试工具 [Phantomjs](http://phantomjs.org/) 这个工具是一个无头浏览器，对于web测试、界面、网络捕获、页面自动化访问等等方面都很强大。基于 Phantomjs 还有相关的 [PhantomCSS](https://github.com/Huddle/PhantomCSS) 帮助做像素的比对测试，和 [PhantomFlow](https://github.com/Huddle/PhantomFlow) 来进行操作对比测试。由于没有太多实践，这里略过。

前端测试方面有的公司会有专门的部门进行测试，但是像我所处的创业公司很少会有专门的测试人员，在测试方面本人的实践经验较少，主要还是在开发中不断的进行人工测试来保证产品质量。但是有时候开发人员的人工测试并不能把所有的 BUG 都抓出来，即使用户发现了 BUG ,又有多少愿意提出 BUG 的呢？所以一个稳定的系统，监控这个环节是非常重要的。

# 前端监控
监控在提高产品稳定和质量方面有很重要的作用。关于前端监控的重要性，可以先看看这篇文章 [不可忽视的前端监控 —— 张鑫](https://zhuanlan.zhihu.com/p/23310438)

在前端监控方面，本人在公司的项目上实验性的部署了 [Sentry](https://github.com/getsentry/sentry) ，这是一个跨平台的错误日志收集系统，不只是Web前端可以使用，其他开发也能使用。引入项目后能够监听到 Web 页面上的 JavaScript 错误，通过邮件及时的通知开发人员进行 Bug 追踪。这个项目是开源项目，可以自己搭建公司的日志管理系统，也可以使用 Sentry 的服务。

### 安装方法
通过CDN方法引入
```html
<script src="jquery.js"></script>
<script src="https://cdn.ravenjs.com/3.8.0/raven.min.js" crossorigin="anonymous"></script>
<script>Raven.config('你的DSN（在后台获取）').install();</script>
<script src="app.js"></script>
```
当然也可以通过 NPM 和 ES6 的语法引入
```javascript
import Raven from 'raven-js';
Raven
    .config('你的DSN（在后台获取）')
    .install();
```

### 如何使用
当你的 JavaScript 代码出现语法错误时，Sentry 会自动帮你上报代码错误。也可以自己将发送报错代码放在 try..catch 块中
```javascript
try {
    doSomething(a[0])
} catch(e) {
    Raven.captureException(e)
}
```
也可以直接抛出错误或者是个打点信息
```javascript
throw new Error('信息')  // 这样写比较好
throw '信息'  // 不建议这样写
```

同时 API 提供错误信息的配置，包括错误等级、注释 log 、 tag 、黑白名单等等，方便错误的追踪统计。
```javascript
Raven.config('你的DSN（在后台获取）', {
    level: 'info' // 可以是 'info'、 'warning'、或者是 'error' 其中一个
    logger: 'my-logger', //log注释类型 
    environment: 'production', //产品环境
    release: '721e41770371db95eee98ca2707686226b993eda', // 当前代码版本
    whitelistUrls: [ 
        /disqus\.com/,
        /getsentry\.com/
    ], 
    ignoreErrors: [
        'fb_xd_fragment',
        /ReferenceError:.*/
    ],
    includePaths: [
        /https?:\/\/(www\.)?getsentry\.com/
    ]
}).install();
```

如果你的 JavaScript 代码是经过代码打包工具压缩的代码，比如 Webpack ，出现错误的时候，会出现提示错误在第一行好几百列的情况，导致代码追踪困难，代码压缩工具一般可以生成 SourceMap 文件，Sentry 默认会在当前 JavaScript 资源路径寻找 SourceMap 文件，如果能请求到，它会解析出源代码，方面追踪错误，但是这会有安全的隐患，就是其他人也能查看并调试你的代码。基于这个问题 Sentry 提供了 SourceMap 上传服务，将产生的 SourceMap 文件通过 API 或者 CLI 进行上传，既解决了错误代码追踪问题又解决了安全问题。

在我们公司项目部署了 Sentry 后，发现一个不足之处，就是没有监测到资源加载错误，所以本人在项目页面中公共头部加入了监听 onerror 事件代码，当资源加载失败时发送错误 Log 给 Sentry ,通过对资源加载情况进行监视，能够及时发现和统计资源的问题。
```javascript
// Record Resources load Errors
window.addEventListener('error', e => {
  if (e.target.src && Pro) {
    Log(`src load error at ${e.target.src}`, {
      logger: 'Resources',
    });
  }
}, true);
```

以上只是调研实践的一点总结，后续会着重研究一下前端自动化测试方面的东西，虽然这个坑很深。

延伸阅读：

http://taobaofed.org/blog/2015/12/10/frontend-test-platform/

http://fex.baidu.com/blog/2015/07/front-end-test/

http://www.alloyteam.com/2014/03/front-end-data-monitoring/

http://fex.baidu.com/blog/2014/05/build-performance-monitor-in-7-days/

https://zhuanlan.zhihu.com/p/23310438

https://www.zhihu.com/question/29953354/answer/128837124





