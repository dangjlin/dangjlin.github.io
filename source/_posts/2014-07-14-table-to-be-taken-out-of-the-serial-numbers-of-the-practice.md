---
layout: post
title: '表格內要帶出序號的做法'
date: 2014-07-14 09:34
comments: true
categories: 
---
需求是這樣的，我希望我的表格的第一個欄位是序號（序列號）從 1 開始一直到整個 object 的迴圈讀完．

簡單查了一下，範例的 source code 如下：
``` ruby 

<h2>查詢記錄</h2>

<table class="table table-bpatented">
  <thead>
    <tr>
      <th>No.</th>
      <th>申請號</th>
      <th>生成時間</th>
    </tr>
  </thead>
  <tbody>
    <% @patents.each_with_index do |patent, i| %>
    <tr>
       <td><%= i+1  %></td>
       <td><%= link_to(patent.apply_no, patent) %> </td>
       <td>11.11.2014 </td>
    </tr>
    <% end %>

  </tbody>
</table>

```

最最關鍵的地方就是在這個 `.each_with_index` 這個 method , 搭配上 iterator 的  `i` 
這樣表格第一欄我們再使用  `i+1` 這樣，就會從 "1 "開始了． 如果沒有加1的話，會從 0 開始顯示．

index 的iterate 變數名稱 一定要放在 |  ,  | 之間的第二個參數位置，例如本例是 `i` 
