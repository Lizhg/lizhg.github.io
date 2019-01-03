---
layout: post
title: "如何在 GitLab 中使用 Git Hooks"
categories: git
---

## GitLab
首先明确：Git 是版本控制系统，GitLab 是基于 Git 的在线仓库管理工具。

## Git Hooks
Git可以在特定的动作发生时触发自定义脚本，比如 `pre-commit` / `post-commit` 等等。
这类特定的动作称作钩子，分为两类：客户端否自和服务端钩子。

## 在部署的 GitLab 服务器中使用 Git Hooks
