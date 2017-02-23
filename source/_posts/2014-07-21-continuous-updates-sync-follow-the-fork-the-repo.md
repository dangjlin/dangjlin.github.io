---
layout: post
title: '持續更新 sync (follow) fork下來的repo '
date: 2014-07-21 17:20
comments: true
categories: 
---
以下是更新的指令，前提是你的版本中沒有尚未commit的東西，不然會更複雜一些。
git remote add upstream "來源的git位置"
git fetch upstream
git checkout master
git merge upstream/master 或 git rebase upstream/master
git push origin (這一步一般是用在更新 github，當然如果有另外的 git server 也有用)

建議還可以參考鴉七寫得很詳細的 git rebase 解說
[Git-rebase 小筆記](http://blog.yorkxin.org/posts/2011/07/29/git-rebase)