---
layout: post
title: 'Agile Web Development with Rails 4 - Error Handling 錯誤或例外處理'
date: 2014-07-27 12:04
comments: true
categories: 
---
書 p125頁，裡面提到，當處理 error handling / exception handling 的時候，有兩個動作可以做
>1. we’ll log the fact to an internal log file using Rails’logger facility.
>2. Second, we’ll redisplay the catalog page along with a short message to the user (something along the lines of “Invalid cart”) so they can continue to use our site.

先說明呼叫 invalid_cart() 這個 action 的作法 

```ruby CartsController
class CartsController < ApplicationController
  before_action :set_cart, only: [:show, :edit, :update, :destroy]
  rescue_from ActiveRecord::RecordNotFound, with: :invalid_cart
  
    private
  # ...
    def set_cart
      @cart = Cart.find(params[:id])
    end

    def invalid_cart
      logger.error "Attempt to access invalid cart #{params[:id]}"
      redirect_to store_url, notice: 'Invalid cart'
    end
    
end
```

當 set_cart 的method 被呼叫的時候，會去執行 Cart.find 的呼叫，當發生找不到的時候，就是 exception 發生了
因此隨即就會去呼叫  invalid_cart 的 method, (因為這個controller一開頭就已經宣告了 ` rescue_from ActiveRecord::RecordNotFound, with: :invalid_cart ` 
因此這個 method 執行的時候，第一步會先log起來，存在 `development.log   in the log directory `
第二步就會發動把 user 的頁面導回去首頁，並且通過 ` notice: 'Invalid cart` 的flash 方式，告訴使用者錯誤訊息是什麼。 

>延伸閱讀
>[http://guides.rubyonrails.org/debugging_rails_applications.html#the-logger](http://guides.rubyonrails.org/debugging_rails_applications.html#the-logger)
