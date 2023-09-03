---
title: Hello Minimalism
date: 2023-09-03 16:59:19
tags:
  - Hello world
---

## npm安装

前往[Node.js官网](https://nodejs.org/en)选择合适版本

## Hexo环境配置

```shell
npm install hexo-cli -g

hexo init blog

cd blog

npm install

hexo server
```

## Hexo基本用法

```shell
hexo server # 本地模拟

hexo new "new post" # 新建一篇文章

hexo clean # 清空缓存
```

## 修改主题

取决于你自己。

## 部署到Github

> 注意您上传的项目名称应为`username.github.io`

1. hexo-deployer-git插件

```shell
npm install --save hexo-deployer-git
```

2. 修改_config.yml文件

```shell
deploy:
  type: git
  repo: xxx.git
  branch: page # 部署到page分支
```

3. 发布到github

```shell
hexo cl # clear cache
hexo g 	# 生成public
hexo d	# deploy
```

4. 修改setting设置中的page中分支为上述分支。

