---
title: Github+Hexo搭建个人博客，备份，SEO优化全纪录
date: 2021-01-01 15:26:05
tags: hexo
---

# Github+Hexo制作个人博客

本篇文章是我搭建个人博客的记录，除了大部分博客中都提到的Github page托管网页静态文件，还实现了源文件备份的功能。

两份重要的文档：Hexo[官方文档](https://hexo.io/zh-cn/docs/)，Next主题[官方文档](https://theme-next.js.org/)。

<!-- more -->

## 安装NodeJS

推荐通过apt安装，[教程](https://github.com/nodesource/distributions/blob/master/README.md)。可以选择LTS(长期支持)源。

``node -v``和``npm -v``有输出就可以。

> npm相关知识，详细的可以看[npm教程](https://www.kancloud.cn/shellway/npm-doc)。本人对前端一窍不通...

## 安装Hexo命令行工具

和一步主要是为了能用全局命令安装hexo基本依赖。

```bash
sudo npm install -g hexo-cli
```

> sudo是为了解决npm全局权限问题，如果觉得不安全可以看上面的npm教程中*解决权限问题*一章。

## 添加github仓库并关联本地源文件

这一步是最关键的，主要是建立一个仓库，把hexo生成的网页静态文件推送到main分支，hexo源文件推送到src分支。实现**一个仓库管理源文件和网页文件**的目的。

1. 在github添加一个仓库，仓库名字为``YOUR_NAME.github.io``，注意**不能**用私人仓库，且勾上**初始化README**文件。

2. 克隆到本地(这里用的是ssh协议，可以免去后续push输密码的麻烦)

   ```bash	
   git clone git@github.com:seanleecn/seanleecn.github.io.git
   ```

 3. 本地建一个src分支，存放源文件，并关联到github的远程src分支

    ```bash
    git checkout -b src  #建分支并切换
    mkdir hexo
    git add .
    git commit -m "create sources branch"
    git push --set-upstream origin src #推送到新的远程src分支
    ```
    
 4. ``cd hexo``->``hexo init``->``npm install``。这三步过后，最基本的Hexo环境就安装好了，``hexo s``可以把博客本地部署到本地4000端口，按照提示打开http://localhost:4000/就能看到效果了。这三条命令的做的内容大致是``hexo init``生成了``package.json``文件，然后npm根据这个文件安装需要的包到node_modules文件夹下面。

##  网页推送到远程main分支

我们需要把Hexo生成的界面推送到github的默认main分支上面，因此需要安装一个git deploy插件

```bash
npm install hexo-deployer-git --save
```

> ``--save``命令会在安装模块到node_modules文件夹同时，把依赖写到package.json文件中，下次执行``npm install``就会安装这个包。

然后在hexo文件夹下面的`_config.yml`中修改

```yaml
deploy:
  type: git
  repo: git@github.com:seanleecn/seanleecn.github.io.git
  branch: main
```

# 博客备份使用说明

至此配置完成了，每次要写博客前，先确认自己在**src分支**和**hexo文件夹路径**下，写好博客，就可以

````bash
hexo clean
hexo g
hexo d
````

推送源文件到main分支

`cd ..`在`YOUR_NAME.github.io`路径下执行

```bash
git add .
git commit -m "更新了XXX"
git push  #会自动推送到src分支
```

这样就实现了**一个仓库管理源文件和网页文件**

## 换电脑之后怎么恢复

类似于安装博客，执行下面的步骤

```bash
git clone git@github.com:seanleecn/seanleecn.github.io.git
cd seanleecn.github.io
git checkout src #切换到src分支
cd hexo
hexo init
npm install
```

> 简要说明一下，由于hexo默认生成了`.gitignore`文件，该文件会在备份源文件的过程中跳过`node_modules`和`.deploy_git`文件夹，也就是hexo的依赖包以及推送到main分支的文件都不会被备份。

# 主题配置的注意事项

1. hexo5.0版本之后支持了npm安装主题，方便升级主题。使用``npm install hexo-theme-next``安装next主题。

2. 由于采用npm安装，整个主题文件都在`node_modules`文件夹下面。为了直接使用升级(`npm install hexo-theme-next@latest`)，建议使用 Alternate Theme Config来配置，也就是不修改`node_modules`文件夹里面的东西，而是使用

   ```bash
   cp node_modules/hexo-theme-next/_config.yml _config.next.yml
   ```
   把主题的配置文件都拷贝出来，修改`_config.next.yml`就可以实现配置主题。

4. _在Hexo/_config.yml修改theme关键字为next,``hexo s``看下效果

# 谷歌SEO优化

> 维基百科：搜索引擎优化（英语：search engine optimization，缩写为SEO），是一种透过了解搜索引擎的运作规则来调整网站，以及提高目的网站在有关搜索引擎内排名的方式。

SEO优化的目的就是为了让搜索引擎能够找到我们的网站，以本博客网站为例，在一开始搭建完成的时候，使用`site:seanleecn.github.io`在谷歌是检索不到我的网页的，因此需要做好SEO优化。

> 由于本人没钱给买个人服务器给网站做CDN，而github域名又拒绝百度的爬虫访问，因此这里只涉及谷歌的SEO。

1. 在`_config.yml`中修改*URL*为自己的网站域名
2. 生成sitemap.xml

    ```bash
    npm install hexo-generator-sitemap --save
    hexo clean
    hexo g #在/public文件夹下面生成了sitemap.xml文件
    ```
3. 前往[Google Search Console](https://search.google.com/search-console/about)填写域名，把选择HTML验证，并把验证内容填到`_config.next.yml`文件中(注意在menu选项中打开sitemap的注释)。