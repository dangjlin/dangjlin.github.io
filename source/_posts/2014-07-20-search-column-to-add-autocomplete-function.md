---
layout: post
title: '搜尋列內增加自動完成功能'
date: 2014-07-20 15:59
comments: true
categories: 
---
此篇文章的練習來自於這篇原作文章 
[Fast autocomplete search term - Rails APP](http://josephndungu.com/tutorials/fast-autocomplete-search-terms-rails)

先大致翻譯一下這篇文章的安裝步驟如下

前言: 要實現在搜尋列內 (Search Form)內快速地跳出跳出猜測的字 (Fast Autocomplete)，並且可以用不同分類的方式呈現，要使用到 NO SQL 資料庫: Redis、RAILS 裡面存取 Redis 的GEM: soulmate ，以及 CSS 和 jQuery 語法來呈現。 
完成的範例可以看這個網站: [seatgeek](https://seatgeek.com/)

1.要先安裝 Redis Server ，Redis 是什麼呢?  是一套開放原始碼的 NO SQL 資料庫 (一種 key-value 的資料庫，或稱為一種資料結構型態的資料庫) ，可以想成進階版的memcached，它提供了更多種資料結構的存放以及Operator 的控制；
主要的特色是 快!! 因為資料會 load 進記憶體後來與呼叫他的程式做IO的反應，所以放在這種要 search auto complete 的地方是相當有效果的。 
MAC 我們可以使用homebrew ，用這個指令安裝 : `brew install redis`

裝好之後呢，就可以打下面的指令來啟動這套資料庫  
> $ redis-server

2.好了，我們可以開一個新的 rails 專案來練習一下這套工具怎麼使用。 
> $ rails new fast-autocomplete 

3.在 Gemfile 裡面增加這些項目，然後 bundle install 
```
gem 'faker', github: 'stympy/faker'
gem 'rack-contrib'
gem 'soulmate', :require => 'soulmate/server'
```
> faker 這套 gem 是待會要用來產生假資料的，使用在 seed.rb 檔案內

4.接著使用 scaffold 快速建造出兩套骨架，用來demo用的
> $ rails g scaffold noun name:string
> $ rails g scaffold verb name:string

5.產生後先執行 rake db:migrate 

6.接著編輯生成假資料的檔案  seeds.rb 
```ruby seeds.rb
# create 300 nouns
puts "Creating nouns"
300.times do
  Noun.create(name: Faker::Hacker.noun)
  Noun.create(name: Faker::Commerce.product_name)
end
 
 
# create 300 verbs
puts "Creating verbs"
300.times do
  Verb.create(name: Faker::Hacker.verb)
  Verb.create(name: Faker::Hacker.ingverb)
end

```
檔案編輯好後 就執行  ` rake db:seed ` 來生成假資料到DB內，這時候**還不是**把資料複製到到 redis 記憶體喔~ 是先在資料庫生成假資料。

7.接著我們將會編輯 Noun 和 Verb 這兩個類型的 model 檔案， 我們會使用 after_save 的 call back 方式來把存到資料庫的資料抄一份去 redis 記憶體上。 什麼時候存過去呢? 就是這段 load_into_soulmate method  以及在資料庫內的資料要被刪除之前， 會使用 before_destroy 的 callback 去把在 redis 記憶體上的資料先移除掉。 是另一段 remove_from_soulmate 的 method 的作用 

程式碼如下 

```ruby noun.rb 
class Noun < ActiveRecord::Base
	after_save :load_into_soulmate
	before_destroy :remove_from_soulmate
 
        validates_uniqueness_of :name
 
	private 
 
	def load_into_soulmate
		loader = Soulmate::Loader.new("nouns")
		loader.add("term" => name, "id" => self.id, "data" => {
			"link" => Rails.application.routes.url_helpers.noun_path(self)
	   	})
	end
 
	def remove_from_soulmate
		loader = Soulmate::Loader.new("nouns")
	    loader.remove("id" => self.id)
	end
end

```

```ruby verb.rb
class Verb < ActiveRecord::Base
	after_save :load_into_soulmate
	before_destroy :remove_from_soulmate
 
        validates_uniqueness_of :name
 
	private 
 
	def load_into_soulmate
		loader = Soulmate::Loader.new("verbs")
		loader.add("term" => name, "id" => self.id, "data" => {
			"link" => Rails.application.routes.url_helpers.verb_path(self)
	   	})
	end
 
	def remove_from_soulmate
		loader = Soulmate::Loader.new("verbs")
	    loader.remove("id" => self.id)
	end
end
```
8.好了，現在我們要來實現把資料庫的資料 load 到記憶體去了，我們的假資料剛剛都已經生成了，那怎麼辦呢?
沒關係，讓我們到 rails console 去，然後分別執行這兩個指令，表示請rails 找出每一筆資料，然後再存檔一次，因此就會觸發 after_save 的 callback method 就自然而然會把資料抄一份到 redis 記憶體了。 
>$ rails console
>2.1.0 :001 >  Noun.find_each(&:save)
>2.1.0 :001 > Verb.find_each(&:save)

我們可以再開另一個terminal 視窗，然後輸入
> $ redis-cli

會叫出 redis 的 console端 
我們可以試著打這個指令: ` hget soulmate-data:nouns 1` or ` hget soulmate-data:verbs 1 ` 看看有沒有出現類似下面的資料，就表示資料有沒有成功的 load 到記憶體內了
>127.0.0.1:6379> hget soulmate-data:nouns 1
>"{\"term\":\"alarm\",\"id\":1,\"data\":{\"link\":\"/nouns/1\"}}
>127.0.0.1:6379> hget soulmate-data:verbs 1
>"{\"term\":\"calculate\",\"id\":1,\"data\":{\"link\":\"/verbs/1\"}}"

----

接著我們來處理讓 rails app 可以讀到 redis 資料的作法 
首先，我們到 route.rb 上，告訴我們的 rails ，有一套 redis 的 client 端在待命著， 

```ruby routes.rb
Rails.application.routes.draw do
  mount Soulmate::Server, :at => "/autocomplete"
 
  resources :verbs
  resources :nouns
 
  root 'home#index'
end
```
改好後重啟server, 
網址打 localhost:3000/autocomplete 看看是否有出現類似這樣的畫面，就表示 soulmate 是否已成功地運行待命著
> {
>   "soulmate": "1.0.0",
>   "status": "ok"
> }

接著，我們再來測試 redis 的資料是不是可以被 soulmate 這個client 套件讀出來，然後呈現到 rails app 網站上
我們可以打 ` http://localhost:3000/autocomplete/search?types[]=nouns&limit=6&term=pro ` 這串字，來測試當我們再搜尋列打上 `pro` 的時候， soulmate 會不會去撈出資料， 類似下面這樣 
![86e50be45a12784301ca852dae49ddb4.png](http://user-image.logdown.io/user/359/blog/359/post/210955/fP5XG53NQOWFiKUAIXrW_86e50be45a12784301ca852dae49ddb4.png)

> 我們是藉由著網址裡面的 "type"去告訴 soulmate 我們要去找哪個 table (或是說存到 redis 上哪個類型的資料) 
> 因此要切記，網址上面的 type 一定要是我們有的 table (或是有存到 redis 記憶體內的類型 ) 

或者我們也可以給定兩種類型，像是這樣 ` http://localhost:3000/autocomplete/search?types[]=verbs&types[]=nouns&limit=6&term=ha  ` 這表示我們要找 noun 類型及 verb 類型內是否有 ha 開頭的字串 ，找到的話會類似下面這樣

![2dcd49da89ef88b4876f05b57dc4fd8b.png](http://user-image.logdown.io/user/359/blog/359/post/210955/NJAMWjxjSdS8n7bp2l4y_2dcd49da89ef88b4876f05b57dc4fd8b.png)

----
好了，終於要來處理前端的畫面了，這邊不用說，就是要套用 css 和 jQuery (Javascript) 的東西
soulmate 很棒的地方就是在於有人寫好了傳送到前端畫面的 js 檔案，
請到這邊抓取壓縮檔，
[soulmate.js @ github ](https://github.com/mcrowe/soulmate.js/archive/master.zip) 
是一個壓縮檔案， 請把目錄`soulmate.js-master/src/compiled `裡面的 `jquery.soulmate.js `檔案放到 assets/javascripts/ 下吧。 
然後在 assets/javascripts/application.js 裡面掛上
```
//= require jquery
//= require jquery_ujs
//= require jquery.soulmate
//= require turbolinks
//= require_tree .
```

壓縮檔裡面的 `soulmate.js-master/demo ` 目錄內有 ` demo.css `的檔案 ，請放到 assets/stylesheets/  裡面，然後改檔案名稱為 `soulmate.css`
然後在 assets/stylesheets/application.css 裡面掛上
```
*= require_tree .
*= require soulmate
*= require_self
*/
```
soulmate.css 檔案內容如下

```css soulmate.css
#soulmate {
    background-color: #fafafa;
    border: 1px solid #bbb;
    display: none;
    font: 12px/14px "Helvetica Neue", Helvetica, Arial, sans-serif;
    font-weight: normal;
    list-style: none;
    margin: 0 0 0 10px;
    padding: 0;
    position: absolute;
    text-shadow: 0 0 0 white;
    /* play around with the top and left properties for correct positioning */
    top: 201px; 
    left: 460px;
    width: 579px;
    z-index: 1000;
    -webkit-border-radius: 4px;
    -moz-border-radius: 4px;
    -ms-border-radius: 4px;
    -o-border-radius: 4px;
    -khtml-border-radius: 4px;
    border-radius: 4px;
    -webkit-box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
    -moz-box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
    -khtml-box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
    -ms-box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
    -o-box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
}
 #soulmate .soulmate-type-container {
    border-top: 1px solid #ddd;
}
 #soulmate .soulmate-type-container:first-child {
    border-top: none;
}
 #soulmate .soulmate-type-suggestions {
    border-left: 1px solid #ddd;
    float: right;
    list-style: none;
    padding: 5px 0;
    width: 490px;
    letter-spacing: 0.5px;
}
 #soulmate .soulmate-suggestion {
    color: #111;
    cursor: pointer;
    font-weight: 500;
    font-size: 13px;
    padding: 5px 0 6px 12px;
    text-decoration: none;
}
 #soulmate .soulmate-suggestion.focus {
    color: white;
    margin-left: -1px;
    margin-right: -1px;
    padding-left: 13px;
    position: relative;
    text-shadow: 0 1px 1px #32629b;
    z-index: 1;
    -webkit-box-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
    -moz-box-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
    -khtml-box-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
    -ms-box-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
    -o-box-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
    background: #545454;
    background: -moz-linear-gradient(top, #545454 0, #444444 100%);
    background: -webkit-gradient(linear, 0 0, 0 100%, from(#545454), to(#444444));
    -ms-filter: "progid: DXImageTransform.Microsoft.gradient(startColorstr=#545454,endColorstr=#444444)";
    filter: progid: DXImageTransform.Microsoft.gradient(startColorstr=#545454,endColorstr=#444444);
}
 #soulmate .soulmate-type {
    background-color: white;
    color: #333;
    font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
    font-size: 11px;
    letter-spacing: 0.5px;
    margin-right: 490px;
    padding: 10px 10px 0 10px;
    text-align: right;
    text-transform: capitalize;
    vertical-align: top;
    -webkit-border-radius: 5px;
    -moz-border-radius: 5px;
    -ms-border-radius: 5px;
    -o-border-radius: 5px;
    -khtml-border-radius: 5px;
    border-radius: 5px;
    zoom: 1;
}
 #soulmate .soulmate-type:before, #soulmate .soulmate-type:after {
    content: "";
    display: table;
}
 #soulmate .soulmate-type:after {
    clear: both;
}
```
----
最後，我們來裝飾查詢門面吧。 
增加一個 controller 
> $ rails g controller home index 

讓網站的 root 指向這個 home controller  & index action 
```ruby routes.rb 
root `home#index` 
```

接著設計一下 view 的畫面 
```ruby index.html.erb
<div class="container">
	<h2>Search Nouns and Verbs</h2>
	<%= form_tag do %>
	  <%= text_field_tag "search", nil, placeholder: "Search" %>
	<% end %>
</div>

```

再裝飾一下版面 
```css home.css.scss
// Place all the styles related to the home controller here.
// They will automatically be included in application.css.
// You can use Sass (SCSS) here: http://sass-lang.com/
.container {
	width: 500px;
	margin: 0 auto;
	padding: 100px;
	text-align: center;
}
 
#search{
	width: 400px;
	padding: 10px;
}
```
因此搜尋的首頁版面會長這樣: 
![3e832a9afea232cd44513215aea070e6.png](http://user-image.logdown.io/user/359/blog/359/post/210955/CMxqIY0jR02iQr4pDKLi_3e832a9afea232cd44513215aea070e6.png)

最後一個步驟，我們要讓首頁有一個 javascript (jQuery) 可以偵測我們在搜尋框打的字，然後接著就會去呼叫 soulmate.js的 javascript 進而開始對 soulmate & redis 做呼叫的動作及接到 server 吐回來的結果，進一步顯示到瀏覽器畫面上
請在 /assets/javascripts/ 裡面增加一個  home.js   (或是把 home.js.coffee 更名為 home.js ) 因為我們要使用的 javascript code 是 pure js code 不是使用  coffee script 
程式碼如下 
```javascript home.js
var ready = function(){
  var render, select;
 
  render = function(term, data, type) {
    return term;
  }
 
  select = function(term, data, type){
    // populate our search form with the autocomplete result
    $('#search').val(term);
   
    // hide our autocomplete results
    $('ul#soulmate').hide();
 
    // then redirect to the result's link 
    // remember we have the link in the 'data' metadata
    return window.location.href = data.link
  }
 
  $('#search').soulmate({
    url: '/autocomplete/search',
    types: ['nouns','verbs'],
    renderCallback : render,
    selectCallback : select,
    minQueryLength : 2,
    maxResults     : 5
  })
 
 
}
// when our document is ready, call our ready function
$(document).ready(ready);
 
// if using turbolinks, listen to the page:load event and fire our ready function
$(document).on('page:load', ready);

```

噹噹!! 大功告成了!!! 如果一切順利的話，當我們在搜尋列打上兩個英文字母，且有match 到資料庫的資料 ( 嚴格說應該是redis 吐回來給 soulmate 再吐給前端 ) ，那麼就會看到類似下面的畫面了。 

![3b21e629c8bf9af09cf67f28f79f5b42.png](http://user-image.logdown.io/user/359/blog/359/post/210955/9VInlFFlTlCe1uVMEzDr_3b21e629c8bf9af09cf67f28f79f5b42.png)

以上，收工。

改天再來寫一篇，如何把這樣的技巧 match 到我們自己的網站。 
我想，秘訣應該是在 model 的 code ，就是要怎麼寫 ruby code 把我們想要作為快速 key-value 比對的資料結構，從資料庫存檔的時候抄寫一份過去呢? 以及要動到 javascript 的東西吧 (頭痛)  好好研究一下。

