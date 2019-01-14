---
layout: post
title: "Git Hooks 的使用"
categories: git
---

## 关于 Git Hooks
Git 是一个分布式版本控制系统，目前最流行的版本控制系统之一。

Git 可以在特定的动作发生时触发自定义脚本，这一类动作称作钩子；钩子分为 Client-Side Hooks 和 Server-Side Hooks 两类，也就是客户端钩子和服务端钩子。其中客户端钩子在本地触发，比如提交时；而服务端钩子则在 Git 服务器中触发，比如接收到来自客户端的推送时。[查看钩子详细分类](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

除了使用 shell，我们也可以选择 Ruby 或者 Python 这些语言来编写脚本。

## 使用 Client-Side Hooks
客户端钩子脚本存储在 Git 项目目录下的 `.git/hooks` 文件夹内。当我们初始化一个项目时，Git 会自动在 `hooks` 目录下创建示例脚本。下面以 `post-commit` 为例。

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

3. 以 Python 为例，在 `post-commit` 文件中写入以下内容
   ```python
   #!/usr/bin/env python
   # -*- coding: UTF-8 -*-

   # 提交时,会在终端打印 hello,git hooks.
   print "hello,git hooks."
   ```

4. 添加可执行权限，以便脚本调用执行
   ```shell
   ➜ ls post-commit -l
   -rw-rw-r--  post-commit

   ➜ chmod +x post-commit

   # post-commit 文件已拥有可执行权限
   ➜ ls post-commit -l
   -rwxrwxr-x  post-commit
   ```

5. 进行 `commit` 操作，触发 `post-commit` 钩子
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

首先介绍一下 GitLab。
GitLab 是基于 Git 的开源在线仓库管理工具，除了基本的 Git 仓库管理，GitLab 还支持多人协作、持续集成等功能。
当前有以下两个版本：
* GitLab Community Edition (CE)，社区免费版
* GitLab Enterprise Edition (EE)，支持额外功能特性的企业版

GitLab 作为 Git 服务器，自然也支持 Server-Side Hooks。下面将讲解如何在 GitLab 服务器中使用 Server-Side Hooks。

1. 进入 GitLab 服务器中对应项目文件夹
   ```shell
   # 替换 group 和 project,注意如果不在该目录的话,则应该是 /home/git/repositories/<group>/<project>.git 目录
   ➜ cd /var/opt/gitlab/git-data/repositories/<group>/<project>.git

   ➜ ls
   HEAD  config  description  hooks  info  objects  refs

   # 在 hooks 文件夹中存放着 GitLab 定义的钩子脚本
   ➜ ls hooks
   post-receive  pre-receive  update
   ```

2. 创建 `custom_hooks` 文件夹用于存放自定义钩子脚本，并创建 `post-receive` 脚本（客户端 push 到 Git 服务器时会触发 `post-receive` 钩子）
   ```shell
   ➜ mkdir custom_hooks
   ➜ cd custom_hooks
   ➜ touch post-receive
   ```

3. 编写 `post-receive` 脚本
   ```python
   #!/usr/bin/env python3
   # -*- coding: UTF-8 -*-

   # 客户端提交时,print 内容发送到客户端,并且在终端打印出来
   print("hello,it's post-receive.")
   ```

4. 将 `post-receive` 所属用户改为 git，同时添加可执行权限
   ```shell
   ➜ chown git post-receive
   ➜ chmod +x post-receive
   ➜ ls post-receive -l
   -rwxr-xr-x 1 git root post-receive
   ```

5. 服务器已经准备就绪，现在回到本地 Git 仓库所在目录，进行 `push` 操作
   ```shell
   ➜ echo "test" >> a.txt
   ➜ git add a.txt
   ➜ git commit -m "test git hooks"

   # remote 表示是来自远程仓库服务器的信息
   ➜ git push
   ...
   Total 3 (delta 0), reused 0 (delta 0)
   remote: hello,it's post-receive.
   To git@x.x.x.x:xxx/xxx.git
   ```

可以看到我们在 `post-receive` 脚本中定义的消息在客户端进行 push 的时候打印出来了，说明客户端 push 时成功触发了服务端的 `post-receive` 钩子。

## 参考链接

* [Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)
* [Custom Git Hooks In GitLab](https://docs.gitlab.com/ee/administration/custom_hooks.html#custom-git-hooks)