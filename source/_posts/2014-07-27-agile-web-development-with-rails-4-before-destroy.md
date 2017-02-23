---
layout: post
title: 'Agile Web Development with Rails 4 - before_destroy'
date: 2014-07-27 10:57
comments: true
categories: 
---
看到在處理刪除產品的時候，在model裡面要多一個 callback 去檢查 購物車裡面的資料
用來確定使用者現在的購物車內沒有這個商品，不然會很嚴重阿，後台把產品刪掉了
結果客戶走流程走去結帳了，那不就會噴ERROR出來? 

這是在上 intermedia class 的時候沒被提到的，趕緊筆記下來，這也就是使用者故事(USER STORY)裡面應該會疏忽的；
```ruby
before_destroy :ensure_not_referenced_by_any_line_item

private 
before_destroy :ensure_not_referenced_by_any_line_item
 private
    # ensure that there are no line items referencing this product
    def ensure_not_referenced_by_any_line_item
      if line_items.empty?
        return true
      else
        errors.add(:base, 'Line Items present')
        return false
      end
    end
```