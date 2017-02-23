---
layout: post
title: '關於view 及helper內跨model 查詢及 ORM object 轉換為array 的作法, view partial 筆記'
date: 2014-08-12 08:09
comments: true
categories:
---
最近在處理一個需求，如果我們有三個 model , 三個table 分別叫做 patents and splitwordAs and splitwordBs
第一個表是用來存所有的查詢資料，包括 id , apply_number, patent_content (文章的欄位) ...etc ..  不過這個表不重要，不是這次的重點
第二、三個表是用來存切割文章用的關鍵詞，但是因為又有必須分階段切割段落的需求，還區分為兩大類， 所以就有splitwordAs & splitwordBs 這兩個表用來存放關鍵詞。  
第二、三個表和第一個表是沒有任何的 association 關聯性的。
一開始為了到底要怎麼在 Controller 內去查詢不同的 model 資料感到痛苦萬分，一直覺得無法完成，原本的觀念以為 abc controller 只能存取 abc model，後來才發現觀念錯誤，根本就異常的簡單。這代表著觀念不清楚就會花很多冤枉路一直查詢。當然如果一個controller 內充滿了去查詢各式各樣的model好像也很不正確，應該可以用別種手法去更精簡的撰寫。 似乎是叫做 decorator or presenter 的 design pettern  ? 不太確定是否有相關。  

言歸正傳，其實只要在 patent controller 裡面下查詢的code，那麼，在 patent 的 view & helper 內就可以任意使用了  
```ruby
	def show
	(前略)
   @first_key = Keyworda.where.not('keyworda' => nil).order('priority asc').pluck(:keyworda)
	 @second_key = Keywordb.where.not('keywordb' => nil).order('priority asc').pluck(:keywordb)
	end
```

而且透過 pluck 的 method, 可以去透過 activerecord 去抓出我們要的欄位內容，比如說，我這邊只需要每個表裡面的每一個關鍵詞， pluck 會回傳為一個 array , 方便我們之後運用。
這邊還要注意， order 的 method 要放在 where之後，最後才能接 pluck 的method ，否則會出現錯誤。


另外，透過這次的實驗，我才知道 controller 裡面去指揮 activereocrd 取到資料之後， view 不用說，當然可以直接用這資料。這次發現 helper 也可以用，真是太棒了。我原本以為 controller 的資料一定必須得先傳去 view , 然後我們必須透過 call method 的方式把 值**當作參數**  傳入 helper 內的 method 。 如果真得必須這樣做，那實在是太麻煩了。
在查詢的過程當中，反而發現其實大多數人比較有問題的是，如何讓屬於 view 的 helper 可以讓 controller 或 model 用呢?

另外筆記一下 view 裡面去 render 兩個區塊，分別是放在不同目錄下的 partial 檔案
我的需求是， 我有一個主版面，要同時顯示兩個不同 model 的 table 資料,  或是 要同時顯示兩個 form 。

```
<h1> 增加斷詞組 </h1>
<div class="row">
  <div class="col-md-6">
    <%= render :partial => "keywordas/new", locals: {keyworda: @keyworda} %>
  </div>
   <div class="col-md-6">
    <%= render :partial => "keywordbs/new", locals: {keywordb: @keywordb} %>
  </div>
</div>
```
這段code表示在另外兩個目錄 `keywordas` , `keywordbs` 有  _new.html.erb 這樣的 partial file
**Partial file 要記得底線!!!**

keywordas/html _new.html.erb

```
<%= form_for(keyworda, url: keywordas_path) do |f| %>
    <%= f.label :keyworda  %>
    <%= f.text_field :keyworda %>
    <%= f.submit " 增加第一步斷詞 ", class: "btn btn-large btn-primary" %>
<% end %>

```
> from rails guide 的說明，放在這邊一起讀作為註解 ，否則每次貼的 code 變數名稱與值都是相同的命名，實在搞不清楚哪個是哪個。
>##3.4.6 區域變數##
>
>要在局部頁面裡使用區域變數，在呼叫局部頁面時使用 :as 選項：
>
```ruby
<%= render partial: "product", collection: @products, as: :item %>
```
>這樣修改以後，可以在局部頁面裡，用 item 來存取到 @products 集合裡的成員。也可以使用 locals: {} 選項，給任何的局部頁面，傳入隨意的區域變數。
```ruby
<%= render partial: "product", collection: @products,
           as: :item, locals: {title: "Products Page"} %>
```
>在這個情況裡，局部頁面裡可以存取到 title 區域變數，值是 "Products Page"。
>Rails 也給傳入集合的局部頁面，提供了一個計數器變數。名稱是集合名加上 _counter。譬如在算繪 @products 時，可以在局部頁面裡使用 product_counter，來知道局部頁面被算繪了幾次。但不能和 as: :value 選項一起使用。

補充說明
collection: 的意思是
>3.4.5 算繪集合
>局部頁面在算繪集合時非常有用。當使用 :collection 選項，會把集合的每個元素插入的局部頁面裡：

html index.html.erb
```
    <h1>Products</h1>
    <%= render partial: "product", collection: @products %>
```

html _product.html.erb
```
    <p>Product Name: <%= product.name %></p>
```
>當局部頁面傳入複數形式的集合時，可以在局部頁面裡透過與局部頁面同名的變數來存取到集合的成員。上例裡，局部頁面是 _product，product 則是當下被算繪的實體。

>算繪集合有簡寫形式。假設 @products 是 product 實體的集合，則在 index.html.erb 可以這麼寫：
```
<h1>Products</h1>
<%= render @products %>
```
>
