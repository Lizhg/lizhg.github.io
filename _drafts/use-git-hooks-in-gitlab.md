---
layout: post
title: "Git Hooks 的使用"
categories: git
---

## 关于 Git Hooks
Git 是一个分布式版本控制系统，目前最流行的版本控制系统之一。

Git 可以在特定的动作发生时触发自定义脚本，这一类动作称作钩子；钩子分为 Client-Side Hooks 和 Server-Side Hooks 两类，也就是客户端钩子和服务端钩子。其中客户端钩子在本地触发，比如提交时；而服务端钩子则在 Git 服务器中触发，比如接收到来自客户端的推送时。[查看详细分类](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

除了使用 shell，我们也可以选择 Ruby 或者 Python 这些语言来编写脚本。

## 使用 Client-Side Hooks
客户端钩子脚本存储在 Git 项目目录下的 `.git/hooks` 文件夹内。当我们初始化一个 Git 项目时，会自动在 `hooks` 目录下创建示例脚本。下面以 `post-commit` 为例。

1. 创建 Git 仓库
   ```shell
   ➜ mkdir test && cd test
   ➜ git init
   ➜ cd .git/hooks
   ➜ ls
   applypatch-msg.sample  pre-applypatch.sample      pre-push.sample
   commit-msg.sample      pre-commit.sample          pre-rebase.sample
   post-update.sample     prepare-commit-msg.sample  update.sample
   ```

2. 创建 `post-commit` 脚本
   ```shell
   ➜ touch post-commit
   ```

3. 以 python 为例，在 `post-commit` 文件中加入以下内容
   ```python
   #!/usr/bin/env python
   # -*- coding: UTF-8 -*-

   # 提交时,会在终端打印 hello,git hooks.
   print "hello,git hooks."
   ```

4. 脚本需要被调用执行，所以为其添加可执行权限
   ```shell
   ➜ ls post-commit -m
   -rw-rw-r--  post-commit

   ➜ chmod a+x post-commit

   # post-commit 文件已拥有可执行权限
   ➜ ls post-commit -m
   -rwxrwxr-x  post-commit
   ```

5. 验证脚本
   ```shell
   # 回到仓库根目录下
   ➜ cd ../../

   ➜ echo "test" >> a.txt
   ➜ git add a.txt

   # 查看是否打印出 hello,git hooks.
   ➜ git commit -m "test git hooks"
   hello,git hooks.
   [master b74c662] test git hooks
    1 file changed, 1 insertion(+)
    create mode 100644 a.txt
   ```

## 使用 Server-Side Hooks

在这之前，先介绍一下 GitLab。
GitLab 是基于 Git 的开源在线仓库管理工具。除了基本的 Git 仓库管理，GitLab 还支持多人协作、持续集成等功能。
当前有以下两个版本：
* GitLab Community Edition (CE)，社区免费版
* GitLab Enterprise Edition (EE)，支持额外功能特性的企业版

GitLab 作为 Git 服务器，自然也支持 Server-Side Hooks。


