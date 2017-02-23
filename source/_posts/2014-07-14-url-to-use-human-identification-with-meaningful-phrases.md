---
layout: post
title: '網址要使用人類可以辨識帶有意義的字句'
date: 2014-07-14 10:04
comments: true
categories: 
---
這個需求應該會是一個普遍上一定會遇到的問題，就是網址不再使用rails因為 RESTful 的設計，不要都是直接抓出各種table的主id鍵值作為KEY值。

查了一些資料之後，發現用最多的應該就是這個gem了 “friendly_ID " 將很多 CASE 都有考量進來。 

當然另外也有土法煉鋼的方式，就是自己create slug 欄位，然後再存檔的時候，先處理字串，再寫到table的欄位去 

先memo第一種做法：friendly_ID 的簡便做法
從官方網站上面節錄下來： [FriendlyId is the “Swiss Army bulldozer” of slugging and permalink plugins for ActiveRecord](https://github.com/norman/friendly_id)

```
# Gemfile
gem 'friendly_id', '~> 5.0.0' # Note: You MUST use 5.0.0 or greater for Rails 4.0+

>rails generate friendly_id
>rake db:migrate

# edit app/models/user.rb
class User < ActiveRecord::Base
  extend FriendlyId
  friendly_id :name, use: :slugged
end

# Change User.find to User.friendly.find in your controller
User.friendly.find(params[:id])

# If you're adding FriendlyId to an existing app and need
# to generate slugs for existing users, do this from the
# console, runner, or add a Rake task:
User.find_each(&:save)

```
這邊有幾個重點：
1. model 裡面把你要置換的網址的欄位寫上去，此範例為使用 User table 的 name 欄位，要用名稱作為網址，而不要使用 xxx/xxx/user/:id  這種網址了。 
2. controller 裡面一定要換成  User.`friendly`.find(params[:id]
這樣 rails 才會認得  xxx/user/name/edit or xxx/user/name/show 這種網址  
3. 當我們要使用這樣的做法去處理每一筆新 create 的資料, FriendlyID都會自動幫我們增加到 slug 的欄位內 
但是舊的怎麼辦呢？  所以就是到 console 裡面使用 ` User.find_each(&:save) ` 這樣的指令去對資料庫的資料重新做一次save,  因為 FriendlyID 會在 before_save 做 validation ，因此發現slug 沒有做，就會重新做一次。 （印象中是這種概念，有錯誤請指正我，謝謝）

第二個方法可以參考 TIM 的筆記，但我也還在摸索當中，暫時還看不太懂。 
[Sluggify Post](http://disco26.logdown.com/posts/171238-30-coder79-day-241-tea-leaf-course-2-week-4-sluggify-posts)
[Better Slug Post](http://disco26.logdown.com/posts/171360-30-coder81-day-242-tea-leaf-course-2-week-4-better-slugs)
[進階：把很多地方要使用Sluggable Title 封裝為 Sluggable Module](http://disco26.logdown.com/posts/171391-30-coder83-day-242-tea-leaf-course-2-week-4-sluggable-module)

另外 stackoverflow 上面也有一篇很有意義的文章
[best way to generate slugs-human readable title in rails](http://stackoverflow.com/questions/1302022/best-way-to-generate-slugs-human-readable-ids-in-rails)

補充另一個網友寫的筆記
[Generate Slug](http://arthurpai.logdown.com/posts/2014/06/21/generate-slug)