---
layout: post
title: 'Agile Web Development with Rails 4 - include CurrentCart (使用SessionID)'
date: 2014-07-27 11:17
comments: true
categories: 
---
在處理把商品放入購物車的時候，我們可以看到 Agile Web Development with Rails 4 裡面的範例是把創造一個購物車拉出去放在 moudle 裡面，然後當需要使用的 controller 要用的時候，會include 包進來，而不會寫死在controller裡面，我猜應該是為了保留彈性與延展性，希望書後面會再提到這個優點是什麼。
跟intermediate 課程的做法不太一樣

書裡面
module 的程式碼如下

```ruby current_cart.rb
#---
# Excerpted from "Agile Web Development with Rails",
# published by The Pragmatic Bookshelf.
# Copyrights apply to this code. It may not be used to create training material, 
# courses, books, articles, and the like. Contact us if you are in doubt.
# We make no guarantees that this code is fit for any purpose. 
# Visit http://www.pragmaticprogrammer.com/titles/rails4 for more book information.
#---
module CurrentCart
  extend ActiveSupport::Concern

  private

    def set_cart 
      @cart = Cart.find(session[:cart_id])
    rescue ActiveRecord::RecordNotFound
      @cart = Cart.create
      session[:cart_id] = @cart.id
    end
end
```
controller 程式碼如下，
所以當我們在把勾選的商品要放入到購物車的時候，會要 create 一個 LineItems 的物件,再把資料存到這個物件內
因此在這個動作前要先去驗證是否已經有購物車的物件的存在，沒有的話就創造一個出來。 
我認為這邊的思維很重要，因為是很抽象，而新手很難聯想到的。  
```ruby LineItemsController
class LineItemsController < ApplicationController
  include CurrentCart
  before_action :set_cart, only: [:create]

 def create
    product = Product.find(params[:product_id])
    @line_item = @cart.line_items.build(product: product)

    respond_to do |format|
      if @line_item.save
        format.html { redirect_to @line_item.cart,
          notice: 'Line item was successfully created.' }
        format.json { render action: 'show',
          status: :created, location: @line_item }
      else
        format.html { render action: 'new' }
        format.json { render json: @line_item.errors,
          status: :unprocessable_entity }
      end
    end
  end
  
```

