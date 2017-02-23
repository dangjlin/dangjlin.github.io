---
layout: post
title: 'Agile Web Development with Rails 4 - 快取緩存 Cache'
date: 2014-07-27 05:23
comments: true
categories:
---
在讀 Dave Thomas 的這本 Agile Web Development with Rails 4 時，有看到以前不懂的觀念跟技巧，筆記下來。

因為做一個購物網站，當使用者很多的時候，會不斷的發出 http request 要求看產品頁面，所以導致我們的app server 就要不斷的去 request 資料庫。  但實際上，我們的產品資訊，在極短的時間內不會一直變動資料，比如說產品的名稱、說明、圖片價格等等，因此延伸出了上 cache 的功能。如果 rails server 去看一下產品資訊的更動時間沒動過，那就直接把cache的資料打回去給 web server 吐回去給使用者；如果有更新過，那就 request 資料庫，要求重新把新的資料傳上來，然後 cache 然後傳回去給 web server .

實際上怎麼做呢?
1 config/envrionments/development.rb 裡面有
`config.action_controller.perfomr_caching ` 的選項，設為 true.
重開 server
2 在 model 的檔案內增加下列的一個 method . 這邊是以產品table 即 Product model 為範例

```
def self.latest
  Product.order(:updated_at).last
end
```

3 接著我們在我們的 view file 裡面，增加一段檢測更新狀態的 CODE
```
<h1>Your Pragmatic Catalog</h1>
<% cache ['store', Product.latest] do %>
  <% @products.each do |product| %>
    <% cache ['entry', product] do %>
      <div class="entry">
        <%= image_tag(product.image_url) %>
        <h3><%= product.title %></h3>
        <%= sanitize(product.description) %>
        <div class="price_line">
          <span class="price"><%= number_to_currency(product.price) %></span>
        </div>
      </div>
    <% end %>
  <% end %>
<% end %>
```
這邊用了一個 rails 所提供的一個技巧，叫做 [ Russian Doll Caching ] 俄羅斯娃娃快取(緩存)
因為我們做了兩層的 cache 設定，使用 [] 中括號
第一層命名為 store , 把整個 Product 的 array 依照更新時間排序後 cache 起來
第二層命名為 entry , cache 每一個 product 的元素
這麼做之後，rails 會打理一切剩下的工作，細節可以來看 rails guide 上的解說 及 DHH 的一篇文章
[caching-with-rails @ rails guide ](http://guides.rubyonrails.org/caching_with_rails.html)
[how-key-based-cache-expiration-works](http://37signals.com/svn/posts/3113-how-key-based-cache-expiration-works)

另外先大推一下 xdite 這篇文章，在講 DHH 如何設計了 cache-digest 的功能使用 MD5 HASH SOURCE CODE 來判斷到底要不要讓 cache expired . 非常厲害
[cache-digest-new-strategy](http://blog.xdite.net/posts/2012/09/03/cache-digest-new-strategy)

>本來想嘗試讀一下 rails guide 上的解說，發現有點太難懂了
>先暫時不詳細解釋 [] 中括號的內容，以及如何清掉cache & update cache的原理。
>讀懂後回來再補上...

最後，要注意的是，我們在開發階段的時候，其實還是要把前述在 config file裡面的值先改回 false,  否則，當我們在調整及修正 view 的template 或內容時，如調整位置、調整表格、CSS等等... 會發現怎麼按 refresh 都沒有用
那是因為 product 的資料沒有更新的時候, webserver 就直接吐快取回給 browser了，他就不去重新request rails server了，這樣反而會造成開發的時候的困擾。
