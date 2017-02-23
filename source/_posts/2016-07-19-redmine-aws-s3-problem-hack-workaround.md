---
layout: post
title: 'Redmine 串 AWS S3 遇到的問題 Hack workaround'
date: 2016-07-19 08:58
comments: true
categories: rails ruby aws redmine
---

整個安裝的步驟，請參考 sdlong 大的文章：

但是一直做到要把 file upload 串到 AWS S3 的時候，我們使用了 gem [redmine_s3](https://github.com/ka8725/redmine_s3)

其中 bundle install 的時候會有一個 error 說重複命名的問題
然後就開始追 code ,  發現是 htmlentities 這個 gem 命名 inodot 變數的時候跟另外一個gem 的命名重複了
雖然人家 htmlentities 在 4.3.4 的時候修正了變數命名重複的問題，
ＢＵＴ
偏偏 redmine_3 的 dependency 又只能綁到 4.3.1
作者有回應說，我沒辦法綁到 4.3.4喔  因為 bulabulabula （省略一百字）


我剛好想到一個方式去 hack 這件事情
預設的 gemfile 會使用 bundler 去讀  rubygems.org 上面的檔案
所以所有的 code & dependency 都會依照 rubygems.org 上面的code所記載的內容運行

我想到我能做的是，我 clone 一份  htmlentities 4.3.1 的 code 回來自己的 github
然後把 lib/htmlentities/mappings/expanded.rb 檔案裡面的重複的  inotod 刪掉～ push 到自己的github.

然後我在 gemfile 裡面去指定  gem 'htmlentities' 的路徑是要去找我的 github
這樣被裝到 system library 的 code 就會被改正的了！

` gem "htmlentities", github: "dangjlin/htmlentities", branch: "fix-inodot-problem"
