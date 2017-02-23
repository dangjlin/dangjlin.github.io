---
layout: post
title: '寫一隻Ruby 檔案定時抓爬網頁資料'
date: 2015-12-03 05:12
comments: true
categories:
---
#如何使用 Ruby 檔案定時抓爬固定網頁的資料
## 固定抓資料的ruby程式碼
你需要的 Ruby Gem 有
`nokogiri`
`mechanize`
`active_record`
假設我們命名成 : ` get_stock_price.rb `。
起手式這樣，如果你不需要寫入資料庫，則可忽視 ActiveRecord 那幾行；但我想通常是需要的。
```ruby get_stock_price.rb
require 'rubygems'
require 'nokogiri'
require 'mechanize'
require 'active_record'

	ActiveRecord::Base.logger = Logger.new(File.open('log/database.log', 'w'))
	ActiveRecord::Base.establish_connection(
	  :adapter  => 'sqlite3',
	  :database => 'db/development.sqlite3'
	)
# 上面請自行更換成你需要的 database adapter

class Futurequote < ActiveRecord::Base
end
```

接著，通常我的固定語法會這樣寫: 我們以抓取台灣股票期貨價格的YAHOO網頁為範例
```ruby get_stock_price.rb
agent = Mechanize.new
page = agent.get("https://tw.screener.finance.yahoo.net/future/aa03?fumr=futurepart")
parse_html = Nokogiri::HTML.parse(page.parser.to_html)
```

接著這邊有點 tricky ,  我們要利用 nokogiri 的 method :  xpath 來定位我們要找的資料欄位 ，這邊是要抓上市的收盤價
通常我都是使用  firefox 裡面的 firebug 套件幫助我去訂出我要鎖定的資料項目的 xpath .

```ruby get_stock_price.rb
close_price_spot = parse_html.xpath("//*[@id='ext-wrap']/table[2]/tbody/tr[1]/td[4]").children.to_s.to_f
```
至於後面會加上 children 則要看每一次 xpath 抓取回傳的資料的結構如何，不見得每次都需要。請自行判斷。
後面的 to_s , to_f  是因為我後續要做運算，所以轉成浮點數型態
我常常都會先省略成下面這一行 ，然後會到 rails console or irb 裡面打 ` close_price_spot.methods` 看看有什麼 method 可以用；或者你要去瀏覽 nokogiri 的說明文件也是可以。 但我都是 try and error 的方式嘗試組出我要的資料內容
```  
close_price_spot = parse_html.xpath("//*[@id='ext-wrap']/table[2]/tbody/tr[1]/td[4]")
```

接下來放一段如何把擷取的資料寫入檔案及資料庫
請先在資料庫內建立好資料表，及所需要的欄位，然後利用 ActtiveRecord 去做ORM的對應
```ruby get_stock_price.rb
      puts close_price_spot
      # write file
		  File.open('tw_future_price.txt', 'a') { |file| file.write("#{close_price_spot}\n") }

		  # write database

		  puts " writing table price "

     		quote = Futurequote.new
		    quote.close_price = close_price_spot
		    quote.save
```
如此就先完成了抓爬固定欄位的資料的 ruby 檔案。

接下來是要如何讓我們的SERVER可以固定時間抓取一次
我們這邊是透過 Rails 的 Rake file 以及 server 本身的 cronjob 的方式來定時執行
這邊的做法其實有很多種，我只是剛好選用了其中一種而已，大家可以再找尋適合自己環境的方法。
## 新增一個 rake file
我們要到 Rails 專案目錄裡面找到 lib/tasks 這個目錄，然後新增一個自己命名的檔案 例如 my_namespace.rake
裡面放上這樣的內容

```ruby my_namespace.rake
namespace :STOCK do

  desc  "go to lookup quote data from yahoo finance tw"
  task :get_stock_price do
  # the ruby file's path is in the project root, the file should be placed in there
  ruby "get_stock_price.rb"
  end

end
```
## 設定固定時間抓取


我這邊有看到有一個 gem 是比較人性化的幫我們去更新 cronjob 的內容
我們在使用了 `whenever` 這個 gem   [github: whenever ](https://github.com/javan/whenever)
whenever 支援的語法有很多種寫法, 可以到 github 那邊看，其中我選用了呼叫 rake 命令的方式
在 Gemfile 裡面加上  gem 'whenever'；
bundle install 後，要做初始化，所以要輸入  `wheneverize .` 這樣的指令
這樣會在 rails 專案目錄裡面的  config 目錄下 產生了一個  `schedule.rb ` 檔案
我們打開這個檔案，修改一下
```ruby schedule.rb
# Use this file to easily define all of your cron jobs.
# It's helpful, but not entirely necessary to understand cron before proceeding.
# http://en.wikipedia.org/wiki/Cron
#   set :output, "log/cron_log.log"
  set :output, { error: 'log/cron_error.log' }

  every 1.day, :at => '06:00 am' do
      rake "STOCK:get_stock_price"
  end
# after alter this file , please use "whenever -w " to write to crontab file
```
這邊要注意的是
1. 裡面的時間是 server 主機的時間!!所以要看一下你的 server 放置國家與你所在國家的時間(例如台灣)是有差別的。
2. 這個檔案儲存後請記得執行 `whenever -w ` 的指令，這樣才會去更新 crontab 檔案

更新後我們在命令列輸入這個指令 ` crontab -l `  可以列出 crontab 目前的內容
shell crontab
```
# Begin Whenever generated tasks for: /home/ubuntu/workspace/config/schedule.rb
0 6 * * * /bin/bash -l -c 'cd /home/ubuntu/workspace && RAILS_ENV=production bundle exec rake STOCK:get_stock_price --silent 2>> log/cron_error.log'
# End Whenever generated tasks for: /home/ubuntu/workspace/config/schedule.rb
```
無意間發現村長大大的筆記，可以變更 whenever 的時區
[http://disco26.logdown.com/posts/284638-whenever-is-set-in-utc-timezone](http://disco26.logdown.com/posts/284638-whenever-is-set-in-utc-timezone)


最後最重要的一點，常常會忘記，就是要叫你的 server 啟動 cron (定時排程)  的功能
我們會下 ` sudo cron start ` 的指令來啟動他
以及可以下  ` ps -aux ` 的指令來檢視是否有啟動了。

有時候很奇怪，時間到了但是不執行
我在 ubuntu裡面，每次 whenever -w 做完之後，我就會多執行一次 cron restart
`sudo service cron restart`

以上就是我如何依據自己的需求實做出來的簡單定時爬蟲。

ps log很重要，剛開始的時候請都要留下紀錄。有log可以幫助查找一開始run的不順的問題

>
補充說明，如果你是使用 Heroku 作為你的正式環境的網站
可以啟用 Heroku Scheduler  的服務，
然後在 Scheduler 的設定頁面上，把指令 `rake "STOCK:get_stock_price"` 的定時頻率時間設定好，這樣也會在背景定時執行了。 我試過了，就算你的 production site 因為 free 的方案而正在睡覺也沒關係。 他還是會乖乖的去寫檔案和寫資料庫。

赫然發現，如果只是要抓固定某個特定網頁的某個特定欄位的話，其實可以用 excel 的工具去抓， 只要解決 "定時" 抓的問題就好。 但這支程式的價值在於 Mechanize 這支 Gem 可以做到模擬人在點擊按鈕後然後送出，接著程式再接回網站回傳的畫面，然後你再繼續刻意設計下一步動作，或要抓取的動態變化的資料。
例如:
1. 如果購物網站的商品的網址是跳動的，每天都在跳動，那麼 excel 的方式就失效了。 但是可能可以透過刻意分析過的程式碼去查詢商品的網址，然後打開網頁，然後抓取固定欄位。
2. 我最想做的一個案例，例如我每次都要查詢三四個網站比較書的價錢，或是查找是否有二手書可以買，以前的做法一定是一個網站一個網站找，但是如果每個網站的查詢頁面都是固定的話，那麼我就可以寫好程式，查一次後讓程式自動去每個網站查一次再把結果排出來。  我想這才是自動化的意義所在。
