---
title: 高频hexo/git命令
date: 2026-06-04 00:02:14
tags: [使用帮助, 软件使用]
categories: 软件工具使用帮助
---
我的个人博客架构与自动化部署流程

本站采用 Hexo + GitHub + Vercel 的现代静态网站架构，实现了“本地写作，云端全自动部署”的高效工作流：

本地创作：在本地电脑使用 Hexo 框架进行博客编写与主题管理。

版本控制：通过 Git 将博客源码（Markdown 文章及配置文件）同步推送到 GitHub 源码仓库。

全自动构建（CI/CD）：Vercel 实时监听 GitHub 仓库动态，一旦捕获代码更新，便自动在云端执行 hexo generate 编译打包。

高效分发：构建完成后，静态页面通过 Vercel 的全球 CDN 进行托管，并完美绑定个人独立域名解析，提供极致的访问速度。


完整指令		简写		核心功能		核心应用场景
hexo new "标题"	hexo n "标题"	新建文章		开始写一篇新博客
hexo clean	无		清理缓存		删文章、改配置、换主题后必跑
hexo generate	hexo g		生成静态网页	将 Markdown 渲染为网页文件
hexo server	hexo s -g		本地预览		在 localhost:4000 实时检查排版
hexo deploy	hexo d		部署上线		推送网页到 GitHub 等云端

博客根目录shift+右键进入powershell，运行以下代码
git add .
git commit -m "更新文章"
git push origin main

当你搭建好 Hexo 博客，或者日常修改了代码后，想要把它同步到 GitHub 仓库，你需要用到 Git 的核心推送命令。

在 Hexo 的世界里，通常有两种“推送”情况。请根据你的需求对号入座：

情况 A：如果你只是想更新博客网站（最常用）
如果你只是写好了新文章，想要让读者在你的博客网站上看到更新，你其实不需要手动敲 Git 命令。Hexo 已经帮你封装好了。

直接在博客根目录下运行 Hexo 的一键部署命令即可：

Git Bash 终端：

Bash
hexo clean && hexo g && hexo d
PowerShell 终端：

PowerShell
hexo clean ; hexo g ; hexo d
背后的原理：当你敲下 hexo d 时，Hexo 会自动在后台调用 Git，把你生成的 HTML 静态网页推送到你配置好的 GitHub 仓库中。

情况 B：如果你想备份博客的“源码”（如 .md 文章、配置、主题）
如果你换了电脑，或者怕电脑坏掉，想要把整个 Hexo 项目的源代码（包括你的 Markdown 原稿、主题文件、站点配置文件）备份到 GitHub 的私有仓库里，就需要用到纯 Git 的推送命令了。

标准的 Git 备份四步法 如下（在博客根目录下操作）：

1. 查看当前状态（可选）
Bash
git status
看看你修改了哪些文件，红色的字代表有新文章或新修改。

2. 把所有修改暂存起来
Bash
git add .
注意 add 后面有一个英文句号 .，代表添加当前目录下的所有变化。

3. 提交到本地仓库，并写上备注
Bash
git commit -m "备份：新增了一篇关于Git推送的博客"
引号里的文字可以随便写，主要是为了以后方便看哪一天提交了什么。

4. 正式推送到 GitHub 云端
Bash
git push origin main
注：如果你的默认分支叫 master，那就写 git push origin master



title: 高频hexo/git命令
date: 2026-06-04 00:02:14
tags: [使用帮助, 软件使用]
categories: 软件工具使用帮助
