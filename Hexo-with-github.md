---
title: 使用GitHub Pages和Hexo搭建个人博客
date: 2025-03-02 23:17:58
tags:
---


## 简介

### GitHub Pages

  GitHub Pages 是GitHub官方的一项免费静态站点托管服务，我们可以托管和发布自己的静态网站页面。由于有GitHub官方背书，可以认为更稳定可靠。

### Hexo

  Hexo 是一个快速、简单、功能强大的博客框架。你使用 Markdown（或其他标记语言）撰写帖子，Hexo 会在几秒钟内生成具有漂亮主题的静态文件。

### Node.js

  Node.js® 是一个免费、开源、跨平台的 JavaScript 运行时环境, 它让开发人员能够创建服务器 Web 应用、命令行工具和脚本。

## 开发环境说明

  Hexo基于Node.js运行，其版本和Node.js的版本有强依赖关系。参考：https://hexo.nodejs.cn/docs/index.html，版本对应关系如下：

  |Hexo 版本|最小（Node.js 版本）|小于（Node.js 版本）|
  |-------------|----------------------|-------------------|
  |7.0+|14.0.0|latest|
  |6.2+|12.13.0|latest|
  |6.0+|12.13.0|18.5.0|
  |5.0+|10.13.0|12.0.0|
  |4.1 - 4.2|8.10|10.0.0|
  |4.0|8.6|8.10.0|
  |3.3 - 3.9|6.9|8.0.0|
  |3.2 - 3.3|0.12|unknown|
  |3.0 - 3.1|0.10 或 iojs|unknown|
  |0.0.1 - 2.8|0.10|unknown|

  本人是在Windows VMware内使用Ubuntu 16.04系统，尝试了Node-v10.15.0、Node-v14.15.0后均出现Hexo的兼容性问题，尝试Node-v22.15.0则出现对Python依赖的错误。请教网络上
  神通广大的网友，并咨询deepseek这位归纳总结小能手，最终确定使用Node-v12.22.12。

  Linux下建议使用源码安装Node，源码包下载链接：https://nodejs.org/dist/v12.22.12/node-v12.22.12.tar.gz。编译命令如下：
    ./configure --prefix=/usr/local && make && sudo make install
  如果系统中有其他版本的Node，请先删除。

## 科学上网设置

  由于众所周知的原因，国内访问github.com等网址会出现连接失败的问题。本人在windows下使用v2ray工具，为了在虚拟机Ubuntu中能正常安装Hexo，详细配置说明如下。

### 虚拟机使用主机代理的设置

  参考https://blog.csdn.net/weixin_63594197/article/details/138069939，主要步骤如下：

  1. 虚拟机网卡使用NAT模式上网，找到主机侧虚拟网卡的IP地址;
  2. 打开v2ray，在配置中找到sock5代理端口，默认是10808；

  如上完成，则后续操作就有了基础。
  例如在虚拟机浏览器的网络设置中，配置sock5代理为<你的主机IP地址>:<v2ray的sock5代理端口>，则你的虚拟机浏览器就可以科学上网了。
  为了简化描述，如下使用“<代理配置>”来代替“<你的主机IP地址>:<v2ray的sock5代理端口>”。

### 配置npm代理

  参考https://cloud.tencent.com/developer/article/1781066，主要步骤如下：

  1. 查看当前http/https代理：
    npm config get proxy
    npm config get https-proxy
  2. 删除现有http/https代理：
    npm config delete proxy
    npm config delete https-proxy
  3. 配置http/https代理：
    npm config set proxy <代理配置>
    npm config set https-proxy <代理配置>

### 配置github代理

  参考https://blog.csdn.net/chenbb8/article/details/127798751，主要步骤如下：

  1. 删除现有代理：
    git config --global --unset http.https://github.com.proxy
  2. 配置http/https代理：
    git config --global http.https://github.com.proxy <代理配置>

  具体配置内容可以通过命令查看：cat ~/.gitconfig

## 安装部署Hexo博客

  终于到了本文的主题。这一步遇到了太多的坑，真的想说Node.js的依赖处理很差劲！
  如下内容参考链接：https://zhuanlan.zhihu.com/p/60578464。

## 安装Hexo

  建议先进行清理操作：

    sudo npm cache clean --force

  使用npm命令安装：

    sudo npm install -g hexo-cli --unsafe-perm

  这里如果不添加“--unsafe-perm”参数，则会有如下错误信息：

/usr/local/bin/hexo -> /usr/local/lib/node_modules/hexo-cli/bin/hexo

> hexo-util@3.3.0 postinstall /usr/local/lib/node_modules/hexo-cli/node_modules/hexo-util
> npm run build:highlight


> hexo-util@3.3.0 build:highlight /usr/local/lib/node_modules/hexo-cli/node_modules/hexo-util
> node scripts/build_highlight_alias.js

events.js:291
      throw er; // Unhandled 'error' event
      ^

Error: EACCES: permission denied, open 'highlight_alias.json'
Emitted 'error' event on WriteStream instance at:
    at internal/fs/streams.js:361:12
    at FSReqCallback.oncomplete (fs.js:156:23) {
  errno: -13,
  code: 'EACCES',
  syscall: 'open',
  path: 'highlight_alias.json'
}
npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! hexo-util@3.3.0 build:highlight: `node scripts/build_highlight_alias.js`
npm ERR! Exit status 1
npm ERR! 
npm ERR! Failed at the hexo-util@3.3.0 build:highlight script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
npm WARN notsup Unsupported engine for hexo-cli@4.3.2: wanted: {"node":">=14"} (current: {"node":"12.22.12","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: hexo-cli@4.3.2
npm WARN notsup Unsupported engine for abbrev@2.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.12","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: abbrev@2.0.0
npm WARN notsup Unsupported engine for hexo-log@4.1.0: wanted: {"node":">=14"} (current: {"node":"12.22.12","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: hexo-log@4.1.0
npm WARN notsup Unsupported engine for hexo-fs@4.1.3: wanted: {"node":">=14"} (current: {"node":"12.22.12","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: hexo-fs@4.1.3
npm WARN notsup Unsupported engine for hexo-util@3.3.0: wanted: {"node":">=14"} (current: {"node":"12.22.12","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: hexo-util@3.3.0
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@~2.3.2 (node_modules/hexo-cli/node_modules/chokidar/node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.3.3: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})

npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! hexo-util@3.3.0 postinstall: `npm run build:highlight`
npm ERR! Exit status 1
npm ERR! 
npm ERR! Failed at the hexo-util@3.3.0 postinstall script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     <你的家目录>/.npm/_logs/2025-03-03T04_40_21_252Z-debug.log

## 初始化Hexo

  依次执行如下命令：

    mkdir <你的blog项目名称>
    cd <你的blog项目名称>
    hexo init
    npm install

  你的项目框架已初始化完成，系统默认带一个Hello World博客页面。

## 本地预览Hexo

  执行如下命令：

    hexo generate

  系统会渲染生成页面。不过不如意的事情总是接着来，你往往没法一帆风顺。这里有一个很坑的问题，错误信息如下：

    INFO  Validating config
    ERROR Plugin load failed: hexo-renderer-marked
    <你的博客目录>/node_modules/marked/lib/marked.cjs:484
        if (cells.length > 0 && !cells.at(-1)?.trim()) {
                                          ^

    SyntaxError: Unexpected token '.'
        at wrapSafe (internal/modules/cjs/loader.js:915:16)
        at Module._compile (internal/modules/cjs/loader.js:963:27)
        at Object.Module._extensions..js (internal/modules/cjs/loader.js:1027:10)
        at Module.load (internal/modules/cjs/loader.js:863:32)
        at Function.Module._load (internal/modules/cjs/loader.js:708:14)
        at Module.require (internal/modules/cjs/loader.js:887:19)
        at require (internal/modules/cjs/helpers.js:74:18)
        at Object.<anonymous> (<你的博客目录>/node_modules/hexo-renderer-marked/lib/renderer.js:3:20)
        at Module._compile (internal/modules/cjs/loader.js:999:30)
        at Object.Module._extensions..js (internal/modules/cjs/loader.js:1027:10)
        at Module.load (internal/modules/cjs/loader.js:863:32)
        at Function.Module._load (internal/modules/cjs/loader.js:708:14)
        at Module.require (internal/modules/cjs/loader.js:887:19)
        at req (<你的博客目录>/node_modules/hexo/dist/hexo/index.js:247:31)
        at <你的博客目录>/node_modules/hexo-renderer-marked/index.js:5:18
        at <你的博客目录>/node_modules/hexo/dist/hexo/index.js:255:20
        at tryCatcher (<你的博客目录>/node_modules/bluebird/js/release/util.js:16:23)
        at Promise._settlePromiseFromHandler (<你的博客目录>/node_modules/bluebird/js/release/promise.js:547:31)
        at Promise._settlePromise (<你的博客目录>/node_modules/bluebird/js/release/promise.js:604:18)
        at Promise._settlePromise0 (<你的博客目录>/node_modules/bluebird/js/release/promise.js:649:10)
        at Promise._settlePromises (<你的博客目录>/node_modules/bluebird/js/release/promise.js:729:18)
        at _drainQueueStep (<你的博客目录>/node_modules/bluebird/js/release/async.js:93:12) 

  具体原因是Node.js版本过低，无法识别代码中的新语法（可选链操作符?. 和 数组的.at() 方法）。但我
  不想升级Node，因为高版本也有这样那样的问题。Deepseek给出的方案是更换渲染器，改用兼容性更好的 hexo-renderer-markdown-it，执行如下操作：

    npm uninstall hexo-renderer-marked
    npm install hexo-renderer-markdown-it --save

  清理缓存后重新生成页面：

    hexo clean
    hexo generate

  启动预览：

    hexo server

  此时系统会提示一个本地网址，你可以通过在本地浏览器访问该网址来预览博客。

## 部署Hexo到GitHub Pages

  本地测试博客没有问题后，就可以发布到远程了。具体步骤如下：

  1. 在你的博客目录执行如下命令：
    npm install hexo-deployer-git --save
  2. 设置本地Git仓库配置
    在GitHub中创建仓库的过程不再赘述，上述的参考链接说的很详细。需要注意的是仓库名称必须为“<你的GitHub用户名>.github.io”。
    打开你的博客目录下的_config.yml文件，在末尾的Deployment部分写入你的Git仓库信息如下：

    deploy:
        type: git
        repository: git@github.com:用户名/用户名.github.io.git
        branch: main

  3. 设置本地Git账号配置
    git config --global user.email "<你的邮箱>"
    git config --global user.name "<你的GitHub用户名>"
  4. 上传网站
    hexo deploy

## 发布自己的文章

  进入你的博客目录，执行：

    hexo new "<你的文章名称>"

  此时在sources/_posts目录会出现一个<你的文章名称>.md文件，你就可以使用markdown编辑器开始编辑你的文章了。
  
  每次文章编写完成后，再执行：

    hexo generate
    hexo deploy

  就可以将你的文章发布到GitHub Pages，然后使用https://<GitHub用户名>.github.io来访问你的博客了。
