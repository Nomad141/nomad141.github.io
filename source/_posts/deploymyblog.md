---
title: 记录我的第一次博客搭建
date: 2021-07-10 13:38:39
tags:
    - 生活记录
    - 技术
    - github
categories:
    - 诺曼の生活经验
post_meta:
  item_text: false
  created_at: true
  updated_at: true
  categories: true
  tags: true
description: 对第一次博客搭建踩坑的相关记录
---

妹有银是不会踩坑滴

<!-- more -->
## 为什么我想要做一个博客
主要是记录生活（文艺复兴）和记录技术文章。其实在大学四年里我反而最需要这么一个可以集中放置技术文章的地方，但是那会我反而忘了这茬，很多参考资料也找不到了。所以我觉得趁着自己还没有正式参加工作，赶紧把部署做好，以后什么都可以往这里写了。
>当然还可以显得很专业（bushi
## 准备？
本次的搭建根据云游君撰写的教程[yunyoujun-教你如何从零开始搭建一个属于自己的网站](https://www.yunyoujun.cn/share/how-to-build-your-site/ "Markdown")进行。同样hexo模板也来自于云游君。（便携小空调yyds）
因此前期工作和部署测试其实均是比较顺利的，真正的问题或者说踩坑发生在尝试进行自动部署的时候。
## 踩坑
### 尝试直接使用yunyoujun的自动部署脚本-无法部署
云游君在教程中表示可以直接使用他的yml文件-[pg-pages.yml](https://github.com/YunYouJun/yunyoujun.github.io/blob/hexo/.github/workflows/gh-pages.yml "Markdown"),稍加修改即可。
文件代码如下。

        name: GitHub Pages
    on:
    push:
        branches:
        - hexo
    jobs:
    deploy:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
            with:
            submodules: true

        - name: Setup Node
            uses: actions/setup-node@v2
            with:
            node-version: "14.x"

        - run: yarn
        - name: algolia
            env:
            HEXO_ALGOLIA_INDEXING_KEY: ${{ secrets.HEXO_ALGOLIA_INDEXING_KEY }}
            run: yarn algolia
        - run: yarn build

        - name: Deploy
            uses: peaceiris/actions-gh-pages@v3
            with:
            github_token: ${{ secrets.GITHUB_TOKEN }}
            publish_dir: ./public
            publish_branch: master
            force_orphan: true

原教程中表示去掉`algolia`部分即可使用。但是我在尝试后，github提示workflow失败，构建出错。
![error1_info](https://i.loli.net/2021/07/10/n2DEpRIzdZWGQ48.png)_上传到github后的wordflow错误提示_
查看报错信息，为我们指向了`.gitmodules`文件，也就是github的submodules功能。
![error2_info](https://i.loli.net/2021/07/10/Clv9eFgOM2oDiH5.png)
按照云游君的说法，我尝试对`.gitmodules`文件整体复制，也尝试了搜索到的各种方法。但是无一例外都无法改变执行失败的结果。由于我对github的一知半解，加上并没有使用过workflow文件，这个问题只有暂时留到后续解决了。
### 尝试按照其他教程-NTR？！
在尝试失败后我把目光转向了云游君文章中的外链[初探无后端静态博客自动化部署方案](https://blog.ichr.me/post/automated-deployment-of-serverless-static-blog/ "Markdown")。这位博主较为详细的解释了GitHub Actions的工作原理和机制，并且同样给出了自己的对应文件。他的代码如下：

    name: Hexo Deploy Automatically

    on: [push]

    jobs:
    build:

        runs-on: ubuntu-latest
        
        strategy:
        matrix:
            node-version: [12.x]

        steps:
        - name: Checkout
            uses: actions/checkout@v2
            
        - name: Use Node.js ${{ matrix.node-version }}
            uses: actions/setup-node@v1
            with:
            node-version: ${{ matrix.node-version }}
        
        - name: Generate
            run: |
            npm i && npx hexo g
        
        - name: Deploy
            uses: peaceiris/actions-gh-pages@v3
            with:
            github_token: ${{ secrets.GITHUB_TOKEN }}
            publish_branch: gh-pages
            publish_dir: ./public
            cname: blog.ichr.me
            force_orphan: true

我尝试修改了其中我能看懂的部分，配置了github的公钥和私钥。这次的运行成功完成了。
*然后我就被NTR了？！*
当我再次尝试使用`nomad41.github.io`也就是我的链接访问博客后，我惊讶的发现我的博客地址会自动跳转到这位博主的首页。这下大条了————我查看了我的github仓库中的`master`分支，发现分支内存放的果然变成了陌生的内容-我把这位博主的博客给用自己的域名部署了一遍。。。
### 尝试恢复
在发现问题后，我尝试彻底删除了`master`分支，重新进行部署，尝试了删除`hexo`分支，重新push并重新部署。均无果，我的url访问仍然会指向这位博主的首页。
这时候我已经有点纳闷了————这件事显得有些邪乎，当我的`master`分支已经不存在那个博客的生成网页后，为什么我还可以用这个url访问到它呢？
于是我彻底删除了`nomad141.github.io`这个代码仓库，并且重建，重新部署。
问题依旧。
于是我掏出手机访问了`nomad141.github.io`，发现它正常的指向了我的主页。
这就有点糟心了。
由于我的计算机通信网络知识并不咋地，所以只能闭着眼撞。在尝试删除cookie、尝试在host文件中直接添加对这个域名的访问，重置ip等一系列操作未果后，我只能将矛头转向申请新的域名这点。这次的域名覆盖也就成了未解之谜————希望随着知识的丰富我可以给出正确的解释和解决方案。
## 认栽，换域名
经过一晚上的折腾后我决定歇逼，转而使用一个新的域名来访问我的博客主页。
于是我到tx云购买了这个域名————第一年一块钱，甚至不够你吃包辣条。
### 将github pages定向到tx云的域名
>这个过程非常简单，网上能找到许多教程。这里仅作总结。

首先完成tx云的实名认证。这个认证有两层-在注册产品时需要一层，而在使用DNS解析服务的时候又需要一次认证。完成以后就可以配置DNS解析服务了。
进入腾讯云的DNSPOD控制台，找到左侧DNS解析-我的域名。记录管理里会有两条NS类型的默认记录，不需要更改。
点击添加记录。这里需要添加两条。在“主机记录”一栏，分别选择`@`和`WWW`，然后“记录类型”选择`A`或者`CNAME`。若选择`A`，需在记录值一栏填入`对应名.github.io`的ip地址（在cmd中ping即可获得），而选择`CNAME`只要填入域名`对应名.github.io`本身即可。当然也可以使用“快速添加解析”功能，只要提供ip地址即可快速添加两条A类型解析。
确认解析正常工作后，进入github的代码仓库。点击`Settings`标签，往下拉找到`pages`，进入GitHub Pages的设置页面。
找到`Custom domain`这个设置项，填入你购买的域名————注意，这里不需要www之类的前缀。直接填写即可。
等待github pages完成配置。即看到绿色的提示：
![hint_green](https://i.loli.net/2021/07/10/q75raPyp3IXeY1B.png)_配置成功_
代表你已经可以使用购买的域名访问博客了。
`Custom domain`项会检查你的域名，并且在底下给出相应的信息。不知道为何，这里提示红色且有DNS错误的提示时不影响访问。猜测是DNS服务器需要等待来完成分配。另外，这里还会提示使用了ip地址而非CNAME来定向网页之类的警告，请酌情进行改进。
>在设置Custom domain项目后，github会在部署的分支下自动生成CNAME文件。你也可以手动在hexo目录的source底下新建一个无拓展名的CNAME文件存放域名，但是有可能产生问题。

### 未解之谜
为什么我的域名会被自动跳转？为什么workflow脚本无法正常执行？workflow的详细语法是什么？这些都需要我进一步的学习尝试后再更新本文。
## 反思总结
一番折腾下来算是完成了博客的基本搭建，可以使用基本功能了。云游君的文档写的已经很细致，只要稍微有一点点开发基础即可顺利阅读。
但是自己带来的问题依然没有解决方案。不过这次接触到的`github pages`、`workflow`、`hexo`等等技术对我来说依然是全新的领域。需要进一步的学习。