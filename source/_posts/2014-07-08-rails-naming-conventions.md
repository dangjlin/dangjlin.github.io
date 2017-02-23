---
layout: post
title: 'rails 命名慣例'
date: 2014-07-08 14:19
comments: true
categories: 
---
此文章是從這個網站來的 ：[Rails中的约定与命名规范](http://dearjohn.iteye.com/blog/1625078)
直接貼過來作為常用的參考
Rails中的约定与命名规范

博客分类： Ruby On Rails
Rails
 
约定优于配置是Rails三大哲学之一。Rails中充满了很多约定，本页面对遇到的约定做一个总结。

单复数的约定
Model用单数因为它表示一个对象如User，
数据库表用复数因为它存放的是对象的集合，
Controller用复数因为它是对对象集合的操作


Routes.rb中定义session一般用resource :session，而不是普通的resources :sessions。因为一般只会操作当前用户的session，不会操作所有session，所以不能定义为复数。
即如果一个请求一个资源时不需要指定ID，就在routes中用单数，如/profile显示当前登录用户的信息，这样你可以使用单数的/profile而不是/profile/:id。
也可以用match “profile” => “users#show”

其它
Controller中可以用变量request，然后可以得到session, request_info, head, method等请求信息
.与#使用惯例：在阅读书时经常会遇到User.all, users#show这样的表示，其中的点.与井号#使用也是有约定的，点.用于调用类方法，井号#用于调用实例方法。

 


最重要的是要一致, 不要一会儿单数, 一会儿复数; 一会儿首字母小写, 一会儿首字母大写. 如果不能做到完全一致, 至少努力做大大部分情况下一致.


变量名的单复数和大小写问题

考虑下面三条命令:
rails generate model user name:string
rails generate controller user --no-test-framework
rails generate integration_test user
其中比较容我困惑的是user这个单词, 出现在不同的场合可能有下面四种情况:
user
users
User
Users
上面的情况在railstutorial这本教程里面出现了至少3种, 在阅读的时候给我造成了很大的困惑. 我想, 到底什么才是最为推荐的? 如果不按照教程上面的例子做会不会有什么问题?

实验的结论:
首字母大小写是无关紧要的, 最终产生的结果是一致的.
model一般都用单数, 复数的model会有歧义, 比如当你创建model的一个实例的时候, 会觉得你在创建多个实例
其它很多地方用复数形式, 比如数据表的名字, 比如routes中的url形式以及url的名称等等.
既然很多地方都用复数, 那么不如统一用复数形式. 另外经过检验, 统一用单数的方式有点行不通, 到restful routes那块跟Rails现有的设计有冲突. 那么索性就统一用复数形式好了.

最终的解决方案: 
大小写问题已经没有争议了, 都用小写即可
单复数问题: model名字用单数, 其它场合都用复数


文件名的单复数问题
经过观察, rails所有的常见的文件名都是小写的. 所以大小写的问题没有争议, 都用小写即可.
但是单复数问题不太统一, 比如users_contoller.rb, model/user_spec.rb.
这边总结的规律是:
跟model相关的东西, 单数居多. 比如model文件, model_spec文件等等
跟controller相关的东西, 复数居多. 比如 users_contoller文件, request/users_spec文件等等.
跟view相关的东西也是复数居多. 因为view的文件的创建是在创建contoller的时候自动创建的, 所以它跟contoller基本上是一致的.比如 javascripts/users.js.coffee
其它场合应该是单复数都可以.
来自：http://www.tylerlong.me/1334585824/  http://rubyer.me/blog/293/