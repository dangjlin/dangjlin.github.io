---
layout: post
title: 'week1 homework note'
date: 2014-06-12 14:05
comments: true
categories: 
---
今天在把原本在 github 上的 branch 要 clone 下來到 nitrous.io 網站的時候發生了一些問題 
先講到底要怎麼去拉自己在遠端的repo branch . 
假設這個在 github 上的repo 叫做 artstore ,
有 Master 的主 branch 以及後來新增加的  add_uploader 這個第二個brnach 
那麼，我在 nitrous.io網站的時候，因為從來沒有同步過這個 repo 
會先下這個指令 
` git clone https://github.com/dangjlin/artstore.git `
這樣就會先把 github 上的這個 master branch 同步回 nitrous.io 

第二步
因為想要去拉 add_uploader 這個 branch 
所以會下這個指令 
` git checkout --track -b add_uploader origin/add_uploader  ` 
其中第一個 add_uploader 是指 nitrous.io 這一端的 branch 的名稱 ,
origin 後面的add_uploader 指的是遠端 github上 這個repo 裡面的 branch 的名稱

之後要push 的時候就一樣可以下
`git push origin add_uploader ` 

接著當然是要先執行一次 bundle install 重新安裝所需要的 gem file, 
然後再執行 rake db:migrate  create 出應該有的 table, 

[補充說明]
因為後來有從 nitrous.io 的網站 push 新的變更到 github 上面
結果我回家使用自己的 macbook air 的時候，勢必是一定要再把變動拉回自己的筆電上 
所以要下這個指令 
`git pull origin add_uploader `

第一個 origin 是要跟 git 說請到遠端去 ( 前提是 remote name 如果沒有變更過的話就會是 origin ) 
第二個 add_uploader 是本地端的 branch name , 不用擔心,  因為前一次筆電端 push 的時候就已經知道 origin 端也有這個 branch name 了， 所以 git 會去遠端把 add_uploader 這個 branch 的資料 拉回來本機端 然後 merge 本機端的 branch 

[補充說明二]
如何追蹤 fork 的 repo 又有新增的 branch , 要新增到自己的 local site, 
可以參考這篇文章寫得很淺顯易懂 
[http://fireqqtw.logdown.com/posts/200871-github-sync-fork-project](http://fireqqtw.logdown.com/posts/200871-github-sync-fork-project)
=== === 

[補充說明三]
這是個不錯的簡表
[How to Git: a Brief Guide to Making Open-Source Contributions ft. Bridge Troll](http://railsbridge.github.io/bridge_troll/)

接著就到了一個卡關的地方，
因為這次的專案在使用者認證系統使用了 `devise` 這個 gem , 所以所有的使用者註冊、權限、驗證、密碼相關問題都由這個 gem去處理。 
因此之前進入 rails console 去 create USER帳號的時候，無論怎麼做都會錯誤，
一開始我用這個指令 : 
```
u = User.new 
u.update_attribute(:email => "xxx@xxx.com" , :encrypted_password => "abcde" ) 
u.save 
```
不用說，這就報 error 了， 因為 1) update_attribute 只能更新一個欄位的資料 , 2) 更新一個欄位的格式要這樣寫 : 
`u.update_attribute(:email, "xxx@xxx.com) `
所以後來我查了一下我又換了一個方式，
```
u = User.new(:email => "xxx@xxx.com", :encrypted_password => "abcde" ) 
u.save 
```
用這個指令也還是報 error，百思不得其解，後來google了才知道因為使用者在進入頁面的時候, rails 會用 devise去處理帳號密碼， password 欄位會用 bcrypt 的加密去 hash 過，然後再去跟資料庫的 User table 的 encrypted_password 欄位對比看是否一致。 所以我在 rails console 裡面直接把 encrypted_password 塞 "abcde " 這樣就被吐error回來了，因為與 bcrypt的 hash 結果格式不相符 [ 這邊是我猜為什麼無法這樣新增的原因 ] 

結果回去看了教材才知道要用這個指令才是正確的
```
u = User.new(:email => "xxx@xxx.com", password => "abcde" , password_confirmation => "abcde" ) 
u.save 
```
這樣一來就會成功的創造一筆資料了， 而後來用 ` u.encrypted_password ` 去看，就會看到 abcde 已經被 hash 過了....
至於為什麼會這樣呢?  我還在找資料，我猜想一定是 device 個 rails gem 的作用，意思是 create user 的時候應該經過 password & password_confirmation 這兩個變數的資料，然後 device 會自動拿 bcrypt 的函示庫去做密碼加密的動作，然後再塞到User Table 的密碼欄位當中。 




基本上一開始的外觀應該就可以直接使用twitter bootstrap 這個超棒的前端 framework 來美化我們的版面 

新手一開始的動作很簡單，有三個步驟

1. 在Gemfile.rb 中添加 bootstrap Gem
`gem 'bootstrap-sass'`

2. 在 app/assets/stylesheets 的  application.css 要加上這一行
``` 
*= require bootstrap 

```

3. 但是因為我發現為什麼下拉式選單竟然跳不下來，原因是因為那是javascript 的效果
所以還要記得在 app/assets/javascrtip 的 application.js 要加上這一行
```
﻿//= require bootstrap
```
這樣之後才可以開始放肆的改各式樣的版面調整了~ 
