---
layout: post
title: "GitHub 秘籍"
description: ""
category: tech 
tags: [Git, GitHub]
---
{% include JB/setup %}

[GitHub秘籍](http://git.io/sheet)收录了一些 Git 和 GitHub 非常酷同时又少有人知的功能。

##Table of Contents

- [GitHub](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#github)
  - [Ignore Whitespace](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#ignore-whitespace)
  - [Adjust Tab Space](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#adjust-tab-space)
  - [Commit History by Author](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#commit-history-by-author)
  - [Cloning a Repository](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#cloning-a-repository)
  - [Branch](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#branch)
    - [Compare all Branches to Another Branch](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#compare-all-branches-to-another-branch)
    - [Comparing Branches](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#comparing-branches)
    - [Compare Branches across Forked Repositories](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#compare-branches-across-forked-repositories)
  - [Gists](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#gists)
  - [Git.io](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#gitio)
  - [Keyboard Shortcuts](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#keyboard-shortcuts)
  - [Line Highlighting in Repositories](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#line-highlighting-in-repositories)
  - [Closing Issues via Commit Messages](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#closing-issues-via-commit-messages)
  - [Cross-Link Issues](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#cross-link-issues)
  - [Locking Conversations](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#locking-conversations)
  - [CI Status on Pull Requests](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#ci-status-on-pull-requests)
  - [Syntax Highlighting in Markdown Files](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#syntax-highlighting-in-markdown-files)
  - [Emojis](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#emojis)
  - [Images/GIFs](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#imagesgifs)
    - [Embedding Images in GitHub Wiki](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#embedding-images-in-github-wiki)
  - [Quick Quoting](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#quick-quoting)
  - [Pasting Clipboard Image to Comments](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#pasting-clipboard-image-to-comments)
  - [Quick Licensing](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#quick-licensing)
  - [Task Lists](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#task-lists)
    - [Task Lists in Markdown Documents](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#task-lists-in-markdown-documents)
  - [Relative Links](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#relative-links)
  - [Metadata and Plugin Support for GitHub Pages](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#metadata-and-plugin-support-for-github-pages)
  - [Viewing YAML Metadata in your Documents](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#viewing-yaml-metadata-in-your-documents)
  - [Rendering Tabular Data](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#rendering-tabular-data)
  - [Revert a Pull Request](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#revert-a-pull-request)
  - [Diffs](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#diffs)
    - [Rendered Prose Diffs](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#rendered-prose-diffs)
    - [Diffable Maps](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#diffable-maps)
    - [Expanding Context in Diffs](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#expanding-context-in-diffs)
    - [Diff or Patch of Pull Request](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#diff-or-patch-of-pull-request)
    - [Rendering and diffing images](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#rendering-and-diffing-images)
  - [Hub](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#hub)
  - [Contributing Guidelines](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#contributing-guidelines)
  - [Octicons](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#octicons)
  - [GitHub Resources](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#github-resources)
    - [GitHub Talks](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#github-talks)
- [Git](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#git)
  - [Previous Branch](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#previous-branch)
  - [Stripspace](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#stripspace)
  - [Checking out Pull Requests](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#checking-out-pull-requests)
  - [Empty Commits](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#empty-commits-trollface)
  - [Styled Git Status](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#styled-git-status)
  - [Styled Git Log](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#styled-git-log)
  - [Git Query](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#git-query)
  - [Merged Branches](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#merged-branches)
  - [Fixup and Autosquash](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#fixup-and-autosquash)
  - [Web Server for Browsing Local Repositories](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#web-server-for-browsing-local-repositories)
  - [Git Configurations](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#git-configurations)
    - [Aliases](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#aliases)
    - [Auto-Correct](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#auto-correct)
    - [Color](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#color)
  - [Git Resources](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#git-resources)
    - [Git Books](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.md#git-books)

##目录

- GitHub
  - 忽略空白字符变化
  - 调整Tab字符所代表的空格数
  - 查看某个用户的Commit历史
  - 克隆某个仓库
  - 分支
    - 将某个分支与其他所有分支进行对比
    - 比较分支
    - 比较不同派生库的分支
  - Gists
  - Git.io
  - 键盘快捷键
  - 整行高亮
  - 用commit信息关闭Issue
  - 链接其他仓库的Issue
  - 设置CI对每条Pull Request都进行构建
  - Markdown文件高亮语法
  - 静态与动态图片
    - 在GitHub Wiki中嵌入图片
  - 快速引用
  - 快速添加许可证
  - 任务列表
    - Markdown文件中的任务列表
  - 相对链接
  - GitHub Pages的元数据与插件支持
  - 查看YAML格式的元数据
  - 渲染表格数据
  - 撤销Pull Request
  - Diffs
    - 可渲染文档的Diffs
    - 可变化地图
    - 在diff中折叠与扩展代码
    - 查看Pull Request的diff和patc
    - 渲染图像发生的变动
  - Hub
  - 贡献者指南
  - GitHub资源
    - GitHub讨论
- Git
  - 前一个分支
  - Stripspace命令
  - 检出Pull Requests
  - 提交空改动
  - 更直观的Git Status
  - 更直观的Git Log
  - Git查询
  - 合并分支
  - 使用网页查看本地仓库
  - Git配置
    - Git命令自定义别名
    - 自动更正
    - 带颜色输出
  - Git资源
    - Git参考书籍