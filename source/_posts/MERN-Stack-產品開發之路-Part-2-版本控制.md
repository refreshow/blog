title: 'MERN Stack 產品開發之路 - Part 2: 版本控制'
date: 2016-07-16 21:45:59
tags: MERN
categories: training
author: gocreating
---

版本控制是產品開發中關乎可維護性的關鍵，現在多數專案開發都已經納入版本控制的觀念了，Refreshow專案也是打從專案的誕生就採用版本控制。

<!-- more -->

## 工具

我們探討的是MERN Stack，一個Modern的Web專案，所以採用的版本控制工具自然非`Git`莫屬了，另外一個常聽見的工具是Subversion，但它基本上是上個世代的產物，所以不予考慮。

Git Local端建議搭配使用GUI程式（如：[Github Desktop](https://desktop.github.com/)），Git Server端則是可以考量專案是否開源，選擇放在[Github](https://github.com/)或是[GitLab](https://about.gitlab.com/)，Refreshow專案是商業軟體，因此我們選擇將程式碼託管於GitLab的Private Repo。

## Git Basics

如果你對於下面幾個常用的指令還不熟悉，那還是趕緊洗洗睡吧，請不要當一個豬隊友！

``` bash
git add .
git commit -m "Commit message"
git push
```

如果上面的指令沒有問題，我們就可以接著討論Commit Message了。

## Commit Message

為了增加版本控制帶來的效率提升，撰寫漂亮的Commit Message乃是關鍵，細節請見[How to Write a Git Commit Message](http://chris.beams.io/posts/git-commit/)

## Gitflow Workflow

在Git的使用慣例上共有4種常見的Workflow，詳細介紹請參考經典教學文[Git Workflows and Tutorials](https://www.atlassian.com/git/tutorials/comparing-workflows)，無論是Refreshow還是其他公司的商業產品，應該都是傾向採用`Gitflow`，詳細內容也請參考[A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)。

在Gitflow中有5種類型的branches：

- master
- develop
- feature branches
- release branches
- hotfix branches
