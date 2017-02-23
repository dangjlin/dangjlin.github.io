---
layout: post
title: '發生在 view內的each 迴圈 -  Rails N+1 queries 問題解法'
date: 2014-07-30 03:27
comments: true
categories:
---
前幾天在 Rails Tuesday 聚會的時候，與一個朋友在討論 code ，本來是在討論 many to many 的 association 時，要如何在中介的table 當中去查詢到另一個table的欄位資料， 例如，有一個table叫做，使用者，一個table叫做商品，我們有使用者可以有很多商品，商品也可以有很多使用者，是多對多的關係，因此我們可能會create 一個中介 table 叫做 user_product ，裡面就只有 user_id & product_id 兩個欄位，  但是如果我們希望可以拿到 product.name 時該怎麼做呢? 或者是如果我們在中介 table 內增加一欄了，那要怎麼拿出這一欄呢?  本篇文章沒有要討論這個問題。

因此拿出了 intermediate 課程當中的購物車的 code ， 當打開放入購物車的 index 頁面 code 時，朋友問我說在view 裡面使用 .each  然後還去關聯另一個table 這樣會造成一直重複查詢，我才想到這可能有**n+1 query (N+1查詢)** 的問題。
經過百思不得其解的奮鬥之後，初步上得到在 view file 內可以使用 includes 的功能去關聯原先沒有拿到的欄位資料，而避免進入到 each 的 iterate 的時候，會重複request 。 以下就是簡單的筆記。

看了兩天的 ActiveRecord Association 發現真的好多東西需要去理解~
html 原本的 cart index view
```
 <tbody>
        <% current_cart.cart_items.each do |item| %>
        <tr>
            <td>
              <%= link_to(item.product.title, admin_product_path(item.product)) %>
            </td>
            <td> <%= item.product.price %> </td>
        </tr>
  </tbody>      

```
原本的 N + 1 queries
```ruby  
Started GET "/carts" for 175.184.245.33 at 2014-07-31 07:13:00 +0000                                                                                                                              
Processing by CartsController#index as HTML                                                                                                                                                       
  Cart Load (0.3ms)  SELECT  "carts".* FROM "carts"  WHERE "carts"."id" = 20 LIMIT 1                                                                                                              
  CartItem Load (0.2ms)  SELECT "cart_items".* FROM "cart_items"  WHERE "cart_items"."cart_id" = ?  [["cart_id", 20]]                                                                             
  Product Load (0.1ms)  SELECT  "products".* FROM "products"  WHERE "products"."id" = ? LIMIT 1  [["id", 8]]
  Product Load (0.1ms)  SELECT  "products".* FROM "products"  WHERE "products"."id" = ? LIMIT 1  [["id", 7]]
  Product Load (0.1ms)  SELECT  "products".* FROM "products"  WHERE "products"."id" = ? LIMIT 1  [["id", 6]]
  Product Load (0.1ms)  SELECT  "products".* FROM "products"  WHERE "products"."id" = ? LIMIT 1  [["id", 5]]
  Product Load (0.1ms)  SELECT  "products".* FROM "products"  WHERE "products"."id" = ? LIMIT 1  [["id", 4]]
  Product Load (0.2ms)  SELECT  "products".* FROM "products"  WHERE "products"."id" = ? LIMIT 1  [["id", 3]]
  Product Load (0.1ms)  SELECT  "products".* FROM "products"  WHERE "products"."id" = ? LIMIT 1  [["id", 2]]
  Product Load (0.2ms)  SELECT  "products".* FROM "products"  WHERE "products"."id" = ? LIMIT 1  [["id", 1]]
```
在 .each 前面，在我們要做 iterate 的 object 後面增加一個 include 作法，讓 Rails 處理 Query 的時候，可以一併一次就先做聯集把 Product 的 detail 資料一起查出來，這樣後面就不用連續查 8 次了，

後來的 cart index view , 增加 : includes(:product)
```html
 <tbody>
        <% current_cart.cart_items.includes(:product).each do |item| %>
        <tr>
            <td>
              <%= link_to(item.product.title, admin_product_path(item.product)) %>
            </td>
            <td> <%= item.product.price %> </td>
        </tr>
  </tbody>             
```

我們可以看到 query 已經從前面的 8+1  變成 剩下 3 個 query , 這麼看可能感受還好，但是當這個畫面是類似查詢訂單那種一撈就是20筆, 40筆, 100筆等地記錄，確實就會大大的把query 從101個降到剩3個而已。

經過增加一個include 後的 query
```ruby
  Cart Load (0.3ms)  SELECT  "carts".* FROM "carts"  WHERE "carts"."id" = 20 LIMIT 1                                                                                                              
  CartItem Load (0.2ms)  SELECT "cart_items".* FROM "cart_items"  WHERE "cart_items"."cart_id" = ?  [["cart_id", 20]]                                                                             
  Product Load (0.4ms)  SELECT "products".* FROM "products"  WHERE "products"."id" IN (8, 7, 6, 5, 4, 3, 2, 1)      

```
其實我也想過 includes 應該是要放在 controller 或 model 裡面，因為畢竟是要處理 request for data 的動作，怎麼會放在view 裡面? 但目前因為這個購物車網站的 controller 內， current_cart 的 object 是放在 application_controller 裡， view 裡面的  @current_cart.cart_items 是靠著 model 的 association 成立的，所以一時之間就找不到地方去放這個 ` includes(:product) ` 了

includes 的用法還有很多，都是為了實現 eager loading ，避免 N+1 queries 的作法。
可以看官方文件 rails api  ActiveRecord 裡面的 ` Eager loading of associations ` 章節
[http://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html#method-i-belongs_to](http://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html#method-i-belongs_to)
或簡單一點的官方文件 rails guide ActiveRecord Query 裡面的 ` N + 1 查询的解决办法 `
[http://guides.ruby-china.org/active_record_querying.html](http://guides.ruby-china.org/active_record_querying.html)

這篇簡單的文章是講放在 controller 裡面的舉例作法
[http://www.sitepoint.com/silver-bullet-n1-problem/](http://www.sitepoint.com/silver-bullet-n1-problem/)

另外值得一提的是， eager loading 也不是聖杯，因為一次做額外的關聯table，會把一大堆東西一次查詢出來，放到 object內，因此memory會被占掉，要怎麼找到一個平衡，就是 performance tuning 的技巧了，待未來有學習到時，再另行寫一篇筆記吧。

#補充說明:#
另外還有一個用法是 joins .
那麼與 includes 的差別在哪呢?
從上述的說明可以看到使用 includes 的時機是，當我們希望先從 db query 兩個關聯的資料表時，在view 裡面 iterate 的時候不希望造成重複的 query . 那是因為我們需要讀取關聯後的另一個 table 的欄位資料，如上述的 product.title 欄位。

但是，如果我們僅僅需要關聯，而讀取的時候不需要讀取另一個table 的欄位資料時，我們則使用 joins 就好了。
可以看這篇的說明
[includes vs joins differences in rails](http://tomdallimore.com/blog/includes-vs-joins-in-rails-when-and-where/)

明顯的兩段code 就可以感覺出來了
```ruby 需要讀取 person table 的 name 的欄位, 所以要使用 includes
Company.includes(:persons).where(:persons => { active: true } ).all

@companies.each do |company|
     company.person.name
end

```
不需要 person table 裡面的欄位,所以使用 joins
```ruby
Company.joins(:persons).where(:persons => { active: true } ).all

@companies.each do |company|
     company.name
end
```
