---
layout: post
title: 'AWS SES ( Simple Email Service) 服務使用'
date: 2016-07-05 03:03
comments: true
categories: 
---
很快地做一個筆記

1. AWS SES 服務目前只有三個 Region （美東 Virginia , 美西 Orggan , 愛爾蘭） 可以用，但是不用擔心，我們開在不同Region的ec2是可調用的 (像我的主機放在東京，上面跑 wordpress ,去呼叫美東的 smtp server 是ＯＫ的。

2. 要使用 AWS SES 服務有幾個動作要做 
 * 你要發出去的 sender 要經過驗證，其實這個很簡單，就是去登記 sender mail address 然後去這個mail 收信點驗證LINK
 * 如果是要認證整個 domain 也可以，就是 dns 那邊要做驗證。
 * 很重要！你要記得去 support center 開一張票，申請 ses mail service increase limitation. 這個很簡單就是幾個問題勾一勾（主要就是問你有沒有讀過使用規定，會不會遵守不寄發垃圾信的規定，會不會處理退信） 以及文字說明為什麼要增加  limitation , 我很簡單寫了需要啟用的原因是 電子商務網站註冊及寄發系統通知使用（當然是要用英文寫） , 其實這封mail 不只是增加 limitation  更是把服務從  sandbox 移到 production 啟用的重要步驟。 
 * 在 SES裡面 create 一組 IAM User，這個時候會產生一組 user name & password  很重要，請記錄下來。 這是後面要拿去 wordpress 做 smtp authentication 用的！ 我一開始傻傻的拿  key id 跟 secret key 去當作帳號密碼一直認證不過～後來才知道是要在 SES 產生一組 user name & password ,  key and secret key 應該是 for REST API 調用的時候要寫到 code裡面的。

3. 還有一個小坑，  我在 wordpress 那邊設定  sender email 舉例: " Daniel@gmail.com " , 但是我在 SES裡面認證過的是 ” daniel@gmail.com “ , 只是因為第一個字母的大小寫不一樣，這樣也會寄信失敗，要注意要一模一樣喔！

其實還有很多別的選擇啦，例如使用 mailchimp , mailgun 等等的 SaaS service. 未來有機會再來比較。 

