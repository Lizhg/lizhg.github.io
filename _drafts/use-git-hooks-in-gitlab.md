---
layout: post
title: "如何在 GitLab 中使用 Git Hooks"
categories: git
---

## Git Hooks
`Git` 是一个分布式版本控制系统，目前最流行的版本控制系统之一。`Git` 的详细介绍以及使用教程请参考官方文档，这里直接进入今天的主题：`Git Hooks`。

`Git` 可以在特定的动作发生时触发自定义脚本，这类特定的动作称作钩子(`hooks`)；钩子分为 `client-side` 和 `server-side` 两类，也就是客户端钩子和服务端钩子。

客户端钩子...
服务端钩子...

以下是具体的分类：
* `client-side`
	+ `pre-commit`
	+ `prepare-commit-msg`
	+ `commit-msg`
	+ `post-commit `
	+ `applypatch-msg`
	+ `pre-applypatch`
	+ `post-applypatch`
	+ `pre-rebase`
	+ `post-rewrite`
	+ `post-checkout`
	+ `post-merge`
	+ `pre-push`
	+ `pre-auto-gc`
* `server-side`
	+ `pre-receive`
	+ `update`
	+ `post-receive`

## GitLab
首先区分一下这两者：Git 是版本控制系统，而 GitLab 是基于 Git 的在线仓库管理工具。




## 在部署的 GitLab 服务器中使用 Git Hooks
