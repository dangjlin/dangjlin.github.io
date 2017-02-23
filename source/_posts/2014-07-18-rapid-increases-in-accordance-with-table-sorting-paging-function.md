---
layout: post
title: '快速把表格增加依照欄位排序、分頁功能'
date: 2014-07-18 06:08
comments: true
categories:
---
之前無意間看到有人推薦了 `jquery-datatables-rails` 這個GEM
是用來快速把一個列表的資料依照欄位排序，或是增加分頁的功能，使用了Jquery的作法
少量的資料可以在 client端排，大量的資料的話應該會造成使用者執行的效能問題
進階作法會是在server side 排序之後才吐回去給前端顯示出來。

說明文件在這裡
[jquery-datatables-rails](https://github.com/dangjlin/jquery-datatables-rails)
同時還參考了 Rails Cast 的解說
[RailsCast #340 DataTables Apr 11, 2012](http://railscasts.com/episodes/340-datatables)

做法真的相當的簡單
1 Gemfile 裡面增加
`'jquery-datatables-rails', '~> 2.1.10.0.3'`
2 安裝這個Gem，執行
`bundle install`
3 在 assets/javascript/application.js 裡面增加:
`//= require dataTables/jquery.dataTables`
要注意的是，如果原本的 application.js 裡面有  `//= require_tree .` 這個包進全部js的內容， 請把上面那行放在 require_tree　前面.
像是這樣
/app/assets/javascripts/application.js

```
//= require jquery
//= require jquery_ujs
//= require dataTables/jquery.dataTables
//= require_tree .
```
4 在 assets/stylesheets/application.css 裡面增加
`*= require dataTables/jquery.dataTables `
如果原本的 application.css 裡面有或 `*= require_tree .` 這個包進全部 css 的內容， 請把上面那行放在 require_tree　前面. 像是這樣:
/app/assets/stylesheets/application.css

```
/*
 * This is a manifest file that'll automatically include all the stylesheets available in this directory
 * and any sub-directories. You're free to add application-wide styles to this file and they'll appear at
 * the top of the compiled file, but it's generally better to create a new file per style scope.
 *= require_self
 *= require dataTables/jquery.dataTables
 *= require_tree .
*/
```
接著在希望可以產生變化的table 所屬的 js 檔案增加:
例如，我希望產生變化的view檔案是在  /app/views/patents/index.html.erb  
所以我就應該去 assets/javascripsts/ 裡面找  patents.js 這個檔案，然後在裡面加上
```javascript
jQuery ->
        $('#patents').dataTable
          sPaginationType: "full_numbers"
```
第二行是指套上 Jquery 的功能，第三行是指再多增加分頁的功能

接著關鍵的地方就是要到view folder 的 index.html.erb 去設定 table 的一些屬性，讓 Jquery 可以找到這個table
原本的table那段的code 是這樣
```ruby
<h2>查詢記錄 </h2>
<table class="table table-bpatented" >

    <tr>
      <th>No. </th>
      <th>申請號</th>
      <th>生成時間</th>
    </tr>

    <% @patents.each_with_index do |patent, i| %>
    <tr>
       <td><%= i+1  %></td>
       <td><%= link_to(patent.apply_no, patent) %> </td>
       <td><%= patent.created_at %> </td>
    </tr>
    <% end %>
</table>
```
有幾個項目要增加上去: 有 `table id ` , `thead `  , `tbody`
因此加上去後會變成
```ruby
<h2>查詢記錄 </h2>

<table class="table table-bpatented" id="patents">
  <thead>
    <tr>
      <th>No. </th>
      <th>申請號</th>
      <th>生成時間</th>
    </tr>
  </thead>
  <tbody>
    <% @patents.each_with_index do |patent, i| %>
    <tr>
       <td><%= i+1  %></td>
       <td><%= link_to(patent.apply_no, patent) %> </td>
       <td><%= patent.created_at %> </td>
    </tr>
    <% end %>

  </tbody>
</table>

```

要注意到 table 的 id  和 .js 檔案裡面的字串是要一樣的，這樣 jQuery 才會對應到是哪個table 要有變化.
如此一來就快速的套進了按照欄位排序，以及有基本的分頁功能了。
