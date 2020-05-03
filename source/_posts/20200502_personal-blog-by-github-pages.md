---
title: GitHub Pages搭建个人博客
categories:
  - 网站搭建
tags:
  - GitHub
abbrlink: 9414
date: 2020-05-02 12:55:16
---
使用GitHub搭建个人博客的过程虽然简单，但是期间还是踩过了一些坑，这里简单的记录一下配置的过程，以方便查找。

<!-- more -->

## GitHub Pages初始化

### 创建个人的GitHub账号

如用户名：`zhangsan`

### 在GitHub账号里新建一个和账号用户名同名的管理仓

如：`zhangsan.github.io`

### 设置该仓为GitHub Page仓

步骤如下：

- 在软件仓的主页面点击`Setting`

- 找到`GitHub Pages`，点击`Choose a theme`

- 然后随便选择一个主题，并对更改进行`commit`

### 在浏览器中输入`https://zhangsan.github.io`便可以访问网页了。

但是这样建好的网站并不美观，下面我们将会使用Hexo提供的框架对网页仓进行优化

## 优化GitHub Pages并对仓库进行管理

参考：[知乎](https://www.zhihu.com/question/21193762/answer/79109280)，[hexo](https://hexo.io/zh-cn/docs/themes.html)

### 搭建的流程

1. `zhangsan.github.io`仓创建两个分支：`master`与`hexo`

2. 设置hexo为默认分支（因为我们只需要手动管理这个分支上的Hexo网站文件）

3. 克隆仓库到本地，并且切换到hexo分支

4. 参考[hexo](https://hexo.io/zh-cn/docs/)安装`node`和`git`，在hexo分支上执部署hexo

5. 修改_config.yml中的deploy参数，即将网页文件上传到`master`上

```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: 'git'
  repo: https://github.com/zhangsan/zhangsan.github.io.git
  branch: master
```

7. 依次执行`git add .`，`git commit -m "..."`，`git push origin hexo`提交网站相关的文件

8. 执行`hexo g`，`hexo d`生成网站并部署到GitHub上

### 关于日常的改动流程

在本地对博客进行修改（添加新博文、修改样式等等）后，通过下面的流程进行管理。

1. 依次执行`git add .`，`git commit -m "..."`，`git push origin hexo`将改动推送到GitHub（分支应为hexo）

2. 执行`hexo g`，`hexo d`发布网站到master分支上

### 本地资料丢失后的流程

当重装电脑之后，或者想在其他电脑上修改博客，可以使用下列步骤：

1. 使用`git clone git@github.com:zhangsan/zhangsan.github.io.git`拷贝仓库，并切换到`hexo`分支

2. 在本地克隆下来的文件夹根目录下，依次执行下列指令：`npm install hexo`，`npm install`，`npm install hexo-deployer-git`，（记得，不需要hexo init这条指令）。

## next主题设置

参考：[next](https://theme-next.iissnan.com/getting-started.html)

- 在`Blogs`目录下，输入命令：

```
git clone https://github.com/theme-next/hexo-theme-next.git themes/next
```

- 打开根目录下的`_config.yml`文件，将theme字段改为next即可

**注意：**

- 项目根目录下的`_config.yml`文件叫作站点配置文件

- 主题文件夹根目录下的`themes/next/_config.yml`文件叫作主题配置文件


### 给next主题添加[标签]等页面

新建页面：

```
$ cd your-hexo-site
$ hexo new page tags

```

设置页面类型：

```
title: 标签
date: 2014-12-22 12:39:04
type: "tags"
---
```

修改菜单：

```
menu:
  home: /
  archives: /archives
  tags: /tags
```

**特别注意：***

`Menu`如果使用了图标，一定要按照下面的格式设置，小心空格

```
menu:
  home: /|| home
  #about: /about/|| user
  tags: /tags/|| tags
  categories: /categories/|| th
```

## 其他

### 将博客文章链接转为静态短地址

安装`hexo-abbrlink`：

```
npm install hexo-abbrlink --save
```

对站点配置文件进行修改

```
abbrlink:
  alg: crc16 #算法选项：crc16丨crc32
  rep: dec #输出进制：dec为十进制，hex为十六进制
```