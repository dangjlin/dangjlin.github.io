---
layout: post
title: 'Rails 串 Stripe 付款的寫 code 幾個步驟（摘要）'
date: 2016-07-15 06:40
comments: true
categories:
---
# Rails & Stripe
先摘要筆記下來，細節有機會再來補．
好好的付款動作，為什麼要跟註冊頁面綁在一起啊～ 然後這個註冊頁面還要跟 multi-tenancy design 綁在一起。 難度上升十倍. 範例也弄太難了吧～ （昏倒狀態）
筆記一下串 Stripe 第三方付款要做哪些動作．

1. 設計 view , 定義好 class
2. 設計 javascript , 該綁定的 , 該防止user誤觸的 ,該跳 error的
3. 設定 secret key 放到 environment variable 或丟去變數管理
4. 寫 model method , 付款動作
5. 寫 controller , 創造 instance variable , call method , redirect .
6. 檢查 strong parameter , 有 whitelist 的話要加上去
