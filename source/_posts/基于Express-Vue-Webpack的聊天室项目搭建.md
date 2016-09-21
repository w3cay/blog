---
title: 基于Express+Vue+Webpack的聊天室项目搭建
date: 2016-09-20 18:26:32
tags: [Express,Vue,Webpack]
---

最近突然想做一个聊天室项目，根据自己学习情况和项目特点，最终决定选择使用 Express、Vue 和 Webpack 来进行项目的搭建。顺便吐槽一下，现在想徒手搭一个前端项目真的比后端环境都难配置，下面记录我的搭建过程和踩坑之路。

首先贴上项目地址 https://github.com/w3cay/ChatGroup

项目托管在Github上，除了添加一些说明文档和版权信息外，首先进 行 Git 的相关配置，在项目的根目录添加了 **.gitgnore** 文件，忽略一些不需要提交变化的文件，主要是 node 生产环境产生的一些文件。
接着，添加一下编辑器的配置，这个主要是为了防止不同编辑器之间的差异，如果是一个人进行开发还好，但是如果需要协作开发的话最好配置一下。在根目录我还添加了一个 **.gitattributes** 文件，主要是优化 **git diff**的体验，忽略编译文件的变化，不然满屏的压缩代码。具体配置请参考 http://www.jianshu.com/p/8ec1b32f2e13 

接下来，初始化node环境，使用 **npm init** 创建一个 **package.json** 文件，在这里进行node的包管理，然后开始安装部署 Express ,通过 Express 生成器生成项目的基本结构。

安装Express生成器

```
$ npm install express-generator -g 
```

生成示例项目并将示例项目和当前项目目录进行整合

```
$ express demo
```

进入项目，安装依赖

```
$ cd Chatgroup
$ npm install
```

初次运行项目,通过 **localhost:3000** 来进行访问

```
$ DEBUG=Chatgroup:* npm start
```

基本结构定好了，但是发现很不爽的事情，node服务每次修改代码，并不能生效，每次都需要进行重启服务器。所有后来又加上了 [nodemon][1] 这个插件，可以在每次修改服务端代码的时候，自动重启服务器，节省了时间，但是感觉体验还不是很好，启动服务器中间还是有间隙的，不能马上生效，所以感觉还是写PHP爽啊！

安装了nodemon之后，启动Express服务器的命令变为

```
$ DEBUG=ChatGroup:* cross-env NODE_ENV=dev nodemon -i src ./bin/www
```

其中 **cross-env** 的作用是解决跨平台设置NODE_ENV的问题

到此，服务端的环境配好了，接下来我开始配置前端的工作流 ，前端开发使用 Vue 框架，JavaScript 采用 es6 语法，Css 使用 scss 语法预编译，使用 webpack 处理语法编译，并且打包。为了解放F5,我在 webpack 中配置了 BrowserSync 插件，通过 **localhost:2333** 进行访问，每次前端代码的改动都会刷新浏览器，这里没有使用webpack的热更新，以后再加上，配置详情直接访问GitHub查看。

```
    plugins: [
        new webpack.optimize.OccurenceOrderPlugin(),
        // new webpack.HotModuleReplacementPlugin(),
        new webpack.NoErrorsPlugin(),
        new webpack.optimize.UglifyJsPlugin({
            compress: {
                warnings: false
            }
        }),
        new ExtractTextPlugin('[name].css'), 
        new BrowserSyncPlugin({
             // browse to http://localhost:3000/ during development,
             // ./public directory is being served
             host: 'localhost',
             port: 2333,
             proxy: 'http://localhost:3000/'
           },      // plugin options
          {
            // prevent BrowserSync from reloading the page
            // and let Webpack Dev Server take care of this
            reload: true
          }
       )
    ]
```

到此，整个项目的前后端环境搭建的差不多了，数据库方面会采用 MongoDB,具体的配置这里就不赘述了，本篇文章不算是严谨的教程，项目并没有搭建的非常完美，此篇文章只是作为我的开发笔记，如有错误欢迎评论指出。☕️


  [1]: http://nodemon.io/