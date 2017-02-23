---
layout: post
title: 'Ruby (Rails) 內運算式 (Operator)的優先順序 " && " , " == " ,  " || " 符號'
date: 2014-07-22 03:31
comments: true
categories: 
---
本篇的概念來自於這篇stackover flow 的文章，筆記一下，否則每次看code都會卡住看不懂，不習慣背後的道理就都會很不直覺阿。 
[When do we use the “||=” operator in Rails ? What is its significance?](http://stackoverflow.com/questions/5691390/when-do-we-use-the-operator-in-rails-what-is-its-significance)

例如:  我們可能會看到這種程式碼，一開始的時候都是無法理解這代表什麼含意，是做了什麼判斷? 傳回什麼值? 誰跟誰比對? 優先順序又是什麼?
```ruby
@_current_user ||= session[:current_user_id] &&
      User.find(session[:current_user_id])
```
我們要先講一個觀念是當看到 ` a ||= b ` 這種code的時候，要知道它其實是 
` a || a = b ` ,
而他所代表的含意是如果 a 本身自己是 nil or false 的時候，則他就會等於 b 的值。 反之，如果 a 不是 nil , 不是 false的時候，那就保持原狀。 
詳細的解說可以看這一篇文章
[What Ruby’s ||= (Double Pipe / Or Equals) Really Does](http://www.rubyinside.com/what-rubys-double-pipe-or-equals-really-does-5488.html)

讓我們來拆解這段程式碼，一步一步的解讀，
> 第一步

```ruby 

@_current_user ||= ( session[:current_user_id] && User.find(session[:current_user_id]) ) 

```
等同於
```ruby

@_current_user || @_current_user = ( session[:current_user_id] && User.find(session[:current_user_id]) ) 

```
代表的是如果 @_current_user 是 nil or false 的時候， 就指定為`||=` 符號右邊的數值，但是右邊又是另一個判斷式，我們進一步解讀。
>第二步

第二個觀念: 這一串  ( session[:current_user_id] && User.find(session[:current_user_id]) ) 是什麼意思?  
這邊有一個小技巧，如果 session[:current_user_id] 不是 nil or false 那麼 ruby 就會去判斷 && 符號的右邊的內容 :  如果  User.find(session[:current_user_id]) 也不是 nil ,這樣的話就會傳回 User.find(session[:current_user_id]) 的值。 

可以參考這一篇 :  [Ruby's && operator for params](http://www.appallingfarrago.com/rails/ruby/&&/2013/11/03/ruby-%26%26-shortcut.html) 術語上似乎是叫做 `short circuit evaluation. `

第三個觀念: 優先順序的問題 
我們可以從 [ruby document](http://ruby-doc.org/core-2.1.2/doc/syntax/precedence_rdoc.html) 看到運算子的優先順序，我將這個表複製如下，規則是 ***越上面的越先運算、執行、判斷*** ( PS: ruby version 2.1.2 )
``` 
!, ~, unary +
**
unary -
*, /, %
+, -
<<, >>
&
|, ^
>, >=, <, <=
<=>, ==, ===, !=, =~, !~
&&
||
.., ...
?, :
modifier-rescue
=, +=, -=, etc.
defined?
not
or, and
modifier-if, modifier-unless, modifier-while, modifier-until
{ } blocks
```

>第三步

所以啦，回到原本的程式碼
當這一串 ` ( session[:current_user_id] && User.find(session[:current_user_id]) )` 先判斷完之後，就知道是 nil 或者是 && 右邊的值，接著再看  || 的左邊 `@_current_user ` 如果不是 nil or false 就決定是保持原狀，否則就把  User.find(session[:current_user_id]) 指定給  @_current_user 。 









      