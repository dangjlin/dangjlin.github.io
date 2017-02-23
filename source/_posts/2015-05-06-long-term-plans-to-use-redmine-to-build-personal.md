---
layout: post
title: '使用 Redmine 來建立個人的長期計畫'
date: 2015-05-06 15:00
comments: true
categories: 
---
為了要可以嘗試使用 redmine 來建立個人計畫，我們得先安裝好 redmine , 
我在網路上看到了這篇文章，一步一步做的確可以成功地建立在 Heroku 上喔。
[如何在Heroku上架設Rdemine服務，使用S3儲存附件](http://blog.enoir.tw/2014/05/21/%E5%A6%82%E4%BD%95%E5%9C%A8heroku%E4%B8%8A%E6%9E%B6%E8%A8%ADrdemine%E6%9C%8D%E5%8B%99%EF%BC%8C%E4%BD%BF%E7%94%A8s3%E5%84%B2%E5%AD%98%E9%99%84%E4%BB%B6/)

另外也可以使用由德國公司開發的 雲端 SaaS Redmine 服務 : `plan.io`  網站:  [plan.io](https://plan.io/)
有提供免費的方案給個人使用，缺點就是唯一這個免費的 Project 會被設定為 Public ,而且不可改為 Private, 
註冊的地方在 Pricing 的頁面，請搜尋 `Bronze` 這個字，
或點這個連結也可以導過去註冊免費方案 => [https://accounts.plan.io/signup/Bronze?locale=en](https://accounts.plan.io/signup/Bronze?locale=en) 

另外在網路上搜尋著 Redmine 之於個人的使用模式及 know how ，發現了下面這篇，因此很快地把他先翻譯成中文讓大家參考。 
原文文章
[個人的な Redmine で長期計画を立てる](http://www.rinist.com/entry/2014/01/27/065119)

## Life Hack ##
如果是置身在軟體開發的世界當中，在公司內使用 Redmine 的人 (或是被強迫使用的)是不少的。使用這個軟體來管理個人的專案，這是非常有用的。 

參考這篇文章
[意外と使われていない「個人用trac」活用のすすめ | ランサーズ(Lancers)社長日記](http://www.web-20.net/2007/12/trac_1.html)
翻譯後的中文文章在這 : [推薦您所忽略的出乎意料地沒在使用的[個人用 trac ] 活用術 ](http://tech.guojheng-lin.com/posts/2015/03/19/surprisingly-not-in-use-trac-using-the-recommended)

## 好的地方 ##

1.可以任意的將票(Ticket)分類 
Ticket 就是 Task 或 Job ，Ticket 的 Tracker , 優先順序 , 任意的分類 , 任意的 item 屬性等等，我們都可以自由的設定與分類。 而且，如果使用了客製化的功能，都可以透過自訂的分類或排序來針對現在的狀態一目了然。但是，在高端的工作(Task)管理軟體例如 Omnifocus ，這也是辦得到的。 

2.建立長期計畫 
這就是把 Redmine 拿來作為個人使用最大的好處了 。 目前現況用來作為TASK管理的工具及軟體，再怎麼樣最多也是只能拿來建立一年兩年左右的長期計劃。 在使用這樣的軟體時，有一些活動，例如 : 「清理冷氣機」、「為了要參加Seminar使用，要製作個人名片」等等這種粒度比較小的專案 (Project) ，使用這樣的工具是便利的。  但是，如果要統合一堆小的專案(or TASK) 變成一個大的計畫或大專案時，例如，要開一場演奏大提琴表演的演奏會、獨立創業，等大計畫，如果可以有 【版本控制( Version ) 】 和【甘特圖 (Gannt Chart)】的概念的話，那麼，使用 Redmine 是會相當便利的。 

3.也可以使用 ネタ帳 (還沒查到什麼意思)
雖然在[意外と使われていない「個人用trac」活用のすすめ | ランサーズ(Lancers)社長日記](http://www.web-20.net/2007/12/trac_1.html)  這篇文章裡面也已經介紹過了，ネタ帳是相當便利的東西。 
使用 Evernote  的方式有很多種，但是只有使用 Tag 和 Notebook　的分類方法，此外，各種不同的idea 的種類要群組起來 (Grouping 起來 ) ，然後要一目了然的依據群族來看是不行的。 因此，使用 Redmine ，把 idea 化作 Ticket 的時候，就可以快速便利的依照分類群組來瀏覽，這樣是非常愉悅的。 當然，每一個各別的 idea 的詳細說明，自然而然就是到 Ticket 的詳細說明欄位去撰寫輸入。 對個人來說， 其次可以用來記錄 idea list 的地方就有 : 手機 App 的開發 List , Blog 的 List 

## 不好的地方  ##

1. 建置 redmine 的程序很麻煩
不論是  IT 工程師，或是不屬於 IT工程師的人，要安裝 Redmine 都是有一點麻煩的。 因為可能為了要達到不論都在哪裡都可以存取這些資料， 因此就考量到必須要有 web server 的建置，這樣的功夫就必須耗費的更大了，造成更多的困難。 

## 個人的使用方法 ##
ticket = mini project 
切票的粒度大小是很困難的。 舉例來說，「買一台新的洗衣機來更換」這樣的一個 project ，這是可以再分解為一些 task的。 
例如: 「測量現在使用的洗衣機尺寸」這樣的一個 task 是不是要在開一張票呢?  在自己使用redmine管理工作時，「買一台新的洗衣機來更換」是不是就只會開一張票而已呢? 如果這件工作「買一台新的洗衣機來更換」，要實際完成的期限是在近期的話，那麼，這件工作就會被移到 Omnifoucs ，然後在 Omnifoucs 上面列出詳細的子項工作。 具體的行動方案的 task list 的管理，我感覺應該是要使用例如 Omnifouct 等等的 task management tool , project 管理 tool . 

## 以月為單位的票管理 ##
再者，不論是這裡，或是[意外と使われていない「個人用trac」活用のすすめ | ランサーズ(Lancers)社長日記](http://www.web-20.net/2007/12/trac_1.html)  這篇文章裡都有介紹了，以月為長度的票管理是比較好的。 【版本】用 "月別" 切換，在月的長度下開票，例如，分別設定了  2014年1月、2014年2月、2014年3月的不同版本，分別把票分配(指派)到各月份去。 此外，2014年4月以後的專案計畫，就放置在 2014年的版本當中。 2015年的專案計畫，就放置在2015年版本當中。  最近的 Task 依照細部的期限去分類，屬於將來的 Task 就使用大的單位去分別分類即可。 



