---
layout: post
title: "PHP 代码规范 Tips"
date: 2018-10-12 20:50:59 +0800
comments: true
categories: phpstorm
---
* list element with functor item
{:toc}

## 前言

团队合作开发，保证代码的规范、统一是一个非常重要的事情。除了有文档明确的说明规范之外，我们还可以利用一些工具来辅助我们轻松实现代码的规范和统一。

<!--more-->

## Phpstorm


1. Phpstorm 依次点击菜单栏 `File -> Settings -> Editor -> Code Style -> PHP` 找到 `Set from` 选择 `Predefinded Style -> PSR1/PSR2`

![](https://i.stack.imgur.com/IhPB8.png)


只有当使用 `Ctrl + Alt + L` 才会按照上面代码风格自动格式化。

2. 录制宏

如果没有使用 `Ctrl + Alt + L` 的习惯的话，你可以使用录制宏的方式，制定『当我们按 `Ctrl + S` 保存的时候自动执行代码格式化』的规则：


- 点击 `Edit -> Macros -> Start Macro Recording` 开始录制宏，然后依次按快捷键 `Ctrl + Alt + L`、`Ctrl + Alt + O` 、`Ctrl + S`。
- 然后点击 `Edit -> Macros -> Stop Macro Recording` 结束录制。会自动弹出一个窗口，自己设置个名称， 比方说 `Super Save`。
- 然后点击 `File -> Keymap` 修改快捷键，把之前保存快捷键修改为 `Alt + S` 或者其他快捷键或者取消。然后搜索 `Super Save` 添加快捷键为 `Ctrl + S` 就可以了。

![568971eb-904f-457b-be4f-0581e9b62315.png](https://i.loli.net/2018/10/12/5bc05565a0157.png)


## [GrumPHP](https://github.com/phpro/grumphp)

通过 [GrumPHP](https://github.com/phpro/grumphp) 配合 Git 强制规范代码格式，格式不通过 Git Commit 执行失败。


## 扩展连接

- [PhpStorm Tips](https://phpstorm.tips/)
- [PHP版的代码整洁之道 中文翻译](https://github.com/php-cpm/clean-code-php)