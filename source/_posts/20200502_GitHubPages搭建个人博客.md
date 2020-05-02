---
title: GitHub Pages搭建个人博客
date: 2020-05-02 12:55:16
categories:
  - 网站搭建
tags: 
  - GitHub
---
博客搭建的过程虽然简单，但是过程很繁琐，这里记录一下简单GitHub Pages博客搭建的过程。

## GitHub Pages初始化

### 创建个人的GitHub账号

如用户名：`zhangsan`

### 在GitHub账号里新建一个和账号用户名同名的管理仓

如：`zhangsan.github.io`

### 设置该仓为GitHub Page仓，步骤如下：

- 在软件仓的主页面点击`Setting`

- 找到`GitHub Pages`，点击`Choose a theme`

- 然后随便选择一个主题，并对更改进行`commit`

### 在浏览器中输入`https://zhangsan.github.io`便可以访问网页了。

但是这样建好的网站并不美观，下面我们使用Hexo提供的框架对网页仓进行优化

## 使用Hexo框架，Next主题对网站进行优化

- [hexo](https://hexo.io/zh-cn/)

- [next](https://theme-next.iissnan.com/getting-started.html)

### 安装`node.js`和`Git`

### Hexo搭建

- 打开命令行，在node中安装Hexo

```
npm install -g hexo-cli
```

- 在任意目录新建文件夹`Blogs`，并对该目录进行初始化

```
hexo init
```

- 测试

```
hexo g // 打包文件
hexo s // 启动服务器
```

### 更换next主题

- 在`Blogs`目录下，输入命令：

```
git clone https://github.com/theme-next/hexo-theme-next.git themes/next
```

- 打开根目录下的`_config.yml`文件，将theme字段改为next即可

**注意：**

- 项目根目录下的`_config.yml`文件叫作站点配置文件

- 主题文件夹根目录下的`themes/next/_config.yml`文件叫作主题配置文件

## 将本地Hexo部署到GitHub Pages

### 修改站点配置文件 _config.yml

```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: 'git'
  repo: https://github.com/zhangsan/zhangsan.github.io.git
  branch: master
```

### 部署

- 在项目根目录下安装Git部署插件

```
npm install hexo-deployer-git --save
```

- 部署到GitHub Pages

```
hexo g 
hexo d
```