---
layout: post
title: "Octopress multi dev env setting step"
date: 2018-05-03 16:23:27 +0800
comments: true
categories: 
---

# 如何在不同的電腦上面重新安裝已經發布過的 Octopress #

## [Deploy] 在其他電腦部署己存在的 octopress ##
```
git clone -b source git@github.com:dangjlin/dangjlin.github.com.git dangjlin.github.io
cd dangjlin.github.io
echo "2.5.1" >> .ruby-version
gem install bundler
bundle install          (才能使用 rake new_post['xxx'] 指令)
git clone git@github.com:dangjlin/dangjlin.github.com.git _deploy
rake generate
rake deploy
```


## [Deploy] 同步不同電腦的 octopress ##

```
git pull origin source      ( 同步 source 的 branch
cd _deploy
git pull origin master      ( 同步 _deploy 目錄的內容
```

ps1: master branch 只存在 _deploy 目錄內
ps2: _deploy 目錄內一定要有一個 CNAME file ，這樣才可以使用自訂的 domain name
所以 rakefile 裡面在做 deploy 的時候，會自動 copy 一個 CNAME file from source branch to _deploy 目錄

