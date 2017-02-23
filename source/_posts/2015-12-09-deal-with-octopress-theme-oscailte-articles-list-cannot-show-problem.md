---
layout: post
title: '解決 Octopress 的 theme Oscailte 文章列表無法出現的問題'
date: 2015-12-09 15:22
comments: true
categories: 
---
#問題: Oscailte 的主題 (theme)安裝後， 部落格頁面無法顯示文章列表

最近自己架設了 Octopress 的部落格，並且放在 github 上吃自己的 domain name. 一切都很順利
但是一直在找好看，符合需求的 theme 來妝點。發現了 Oscailte 的時候很開心覺得做得真好；又有 index 可以做為landing page 頁面，也有 blog 頁面。

[oscailte preview site](https://coog.ie/blog/)
[oscailte github](https://github.com/coogie/oscailte/)

但是安裝好之後不知道為什麼，部落格首頁的左半邊應該是要出現文章列表才對! 但是就是空白出現不了! 
一開始一直以為是 source 目錄裡面的 template 問題。研究了好幾天，但實在太難懂了  不知道從何改起。 

後來才發現問題竟然是出現在 pagination 的參數。 
如果你也有一樣的問題

請打開   `_config.yml` 檔案
找到這一行 `paginate_path` 
本來預設是寫 `posts/:num`
我把它改為 `blog/:num` 

一切就都正常了!!!


```ruby _config.yml
前略

paginate: 10          # Posts per page on the blog index
paginate_path: "blog/:num"  # Directory base for pagination URLs eg. /posts/2/
recent_posts: 5       # Posts in the sidebar Recent Posts section
excerpt_link: "Read on &rarr;"  # "Continue reading" link text at the bottom of excerpted articles
excerpt_separator: "<!--more-->"

```

我自己的站需要把 本來的 /blog/:year/..... 改為 /posts/:year/....
所以另外測試了，就算 perment link 改動，也不會影響到 paginate_path。 還是要維持在 `blog/:num` 喔!!

```ruby _config.yml
# If publishing to a subdirectory as in http://site.com/project set 'root: /project'
root: /
permalink: /posts/:year/:month/:day/:title/
source: source
destination: public
plugins: plugins
code_dir: downloads/code
category_dir: blog/categories

```
