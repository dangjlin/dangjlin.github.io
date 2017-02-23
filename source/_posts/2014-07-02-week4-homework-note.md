---
layout: post
title: 'Week4 Homework Note'
date: 2014-07-02 14:01
comments: true
categories: 
---
這是 xdite 的 Ruby on Rails intermidate class 的第四堂課
主要是在講解一些進階的技巧，例如 rake 這個工具, 前端後端加速優化的技巧, 利用 capistrano deploy 自己的網站, 還有講解求職的一些 Tips. 

剛剛先寫了一個簡單的 rake file 的作業 

`利用 Rake 建立 10 個User 假帳號`

簡單的嘗試了一下,程式碼如下, 請從第三個 desc "build account" 開始看。 

`desc`  用來描述這個 rake 工作是做什麼的
`task` 用一個 symbol 來命名這個 rake task ,  搭配範例中第一行的 `namepace 為 :dev ` ,  因此在 shell 端就是直接打  `rake dev:build_acc` 這樣的指令 就會去執行我要他做的動作。
經過這樣測試之後，看起來是可以直接把原本在 rails console 要做的指令，搬過來，然後加上迴圈或判斷式加工，就可以做到很多我們想做的動作了。 

```ruby
namespace :dev do
  
  desc "Rebuild system"
  task :build => [ "tmp:clear", "log:clear", "db:drop", "db:create", "db:migrate", "db:seed" ]
  
  desc "demo"
  task :demo => :environment do
    for i in 1..10 do
      puts i 
    end 
  end
  
  desc "build_account"
  task :build_acc => :environment do
    for n in 1..10 do
      User.create!(:email => "abc#{n}@xxx.com", :password => "12345678", :password_confirmation => "12345678")
    end
  end
  
end



```

