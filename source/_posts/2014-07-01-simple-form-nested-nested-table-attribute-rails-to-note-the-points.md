---
layout: post
title: 'simple_form nested attribute 巢狀表格 Rails 要注意的點'
date: 2014-07-01 03:56
comments: true
categories:
---
會有這一篇解bug的紀錄是因為發現自己在 edit product 的page 裡面，使用了 simple_form_for 的巢狀表格時，增加引入了 simple_fields_for 這個東西， 結果出現多個上傳照片的按鈕，這很明顯一定是一個bug.

後來查到這一篇在 stackoverflow 上的解法，才知道，應該是第二層的 instant variable 沒有被載入，所以就會create出新的。
正確的code應該這樣寫:

剛開始的時候使用 `simple_form_for [:admin, @product ] ` 這樣的句法去載入在 namespce admin 下的 @product 變數。
在第二層的時候使用 ` simple_fields_for @product, :photos do |c| ` 這樣的句法去載入 @product 這個變數,
本來沒寫的時候, 你會發現每次網頁送出一次後，下次再進來就會出現兩列要上傳圖片的列, 看起來就不正確,不論重新上傳多少張,都應該要只有一列而已。 原因就是出現在第二層的表格,他不知道已經有產生過了


```
<h2> 新增產品 </h2>

<%= simple_form_for [:admin, @product ] , :html => { :class => "form form-horizontal"  } do |f| %>

  <div class="form-group">
      <div class="col-sm-10">
      <%= f.input :title %>
    </div>
  </div>

  <div class="form-group">

    <div class="col-sm-10">
      <%= f.input :description, :as => :text %>
    </div>
  </div>



  <div class="form-group">

    <div class="col-sm-1">
      <%= f.input :quantity %>
    </div>
  </div>


  <div class="form-group">
      <div class="col-sm-1">
      <%= f.input :price %>
    </div>
  </div>

    <%= f.simple_fields_for :photos do |c| %>
      <%= c.input :image , :as => :file %>
    <% end %>



  <div class="form-group">
    <%= f.submit "Submit", :class => "btn btn-default" , :disable_with => 'Submiting...' %>
  </div>




<% end %>





```
