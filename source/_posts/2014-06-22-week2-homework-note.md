---
layout: post
title: 'week2 homework note'
date: 2014-06-22 14:15
comments: true
categories: 
---

上課的時候有講到一個技巧，如果 controller 的 method 要給 view 用的時候
可以使用 ` helper_method ` 這個技巧

在 controllers/application_controller.rb  可以放入這段 CODE 
``` ruby
 helper_method :current_cart
 
  def current_cart
    @current_cart ||= find_cart
  end
 
  def find_cart
 
    cart = Cart.find_by(id: session[:cart_id])
 
    unless  cart.present?
      cart = Cart.create
    end
 
    session[:cart_id] = cart.id
    cart
  end
```

其中一開始看不懂的是這個符號  " ||= "  這什麼鬼阿!
可以看這篇的解釋，
[ 解釋 ||= 這個符號的意義 ](http://rubyer.me/blog/568/)

因為現在是 many to many 的關聯性 ,
剛剛採了一個雷
就是在找購物車的商品的時候，我傳遞了選擇的 product id 去 carts 的 controller, 把 product.id 帶過去
然後因為我要在 cart_items 這個 table 去找出商品
所以一開始這樣寫 
>def remove_item
>
>  @item = `Cart_Item`.find_by(product_id: params[:id]) 
>  if @item.destroy 
>    flash[:notice] = "你已成功移除這項商品"
>    else
>      flash[:warning] = "Something Wrong when remove item!"
>   end
>    redirect_to carts_path
>end


(sorry 為了凸顯那個我錯的地方 ,所以用引用的方式show 程式碼 , 結果無法縮排 )
雖然 table name 是  cart_items ,  但是在上面的時候正確應該是要打   @item = `CartItem`.find_by(product_id: params[:id])
這應該是命名慣例阿~ 踩過一次就記得了吧


問題好多
1. 信用卡付款之後跳轉訂單畫面，但是應該要清空購物車 ，想法是可以先去走 cart/destory 的controller , 但是裡面會跳出 你已成功清除購物車 ,  理論上結帳過來的動作不應該跳出這段字才對
2. 不同USER的訂單編號應該都要從 1 開始.... 而不是直接顯示 order table 裡面的編號 , 那是全部系統訂單的編號
3.
4.  如果已經變更了 aasm 裡面的狀態，結果要變更第二次的時候就噴error了，所以應該要有 error handling 的機制
