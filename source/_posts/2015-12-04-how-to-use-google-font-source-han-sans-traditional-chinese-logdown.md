---
layout: post
title: '在部落格使用 Google 思源字體　　　( logdown 篇 )'
date: 2015-12-04 01:26
comments: true
categories: 
---
# 新細明體與黑體的字呈現在網頁或PowerPoint上的視覺感受不同

## 新細明體與黑體的超級比一比
先看看差別，左邊是預設的新細明體(我用firefox的網頁工具箱看，顯示為PMingLiu) ，右邊是 Google 的思源黑體
不知道你們看起來感覺哪一邊比較舒服呢? 

![compare.png](http://user-image.logdown.io/user/359/blog/359/post/341822/gh1rwq9STOeJX5h1QI0p_compare.png)

之前使用過 justfont 的 webfont 試用，感覺不錯。
但是因為目前本部落格流量也沒很大，所以覺得暫時不需要每月付費租用；無意間搜尋別的解法的時候，發現了 adobe 台的字型平台 **[typekit](https://typekit.com/)**有提供 free tier 可以每月使用。 free tier 每月的瀏覽量是 25,000 PAGEVIEW. 如果有超過的話我再來煩惱 :p 

##申請帳號及字型
註冊好帳號之後
第一步就是 Create new kit 這時候會跳出另一個新視窗
![setting1.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/mLH18btoSvyEnC8Sre3U_setting1.PNG)
第二步，填寫這個 kit 的名稱以及很重要的要把自己的網站的 domain name 填上去 
像我的站我就填上了   tech.guojheng-lin.com 
![setting3.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/DfZB0joGTsi6rRJo54WX_setting3.PNG)
第三步，我們先直接按 continue 及 browse all fonts 的按鈕，回到原本的視窗的網頁。
![setting4.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/G6Biss7SC6ox9GciN2cb_setting4.PNG)
![setting5.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/NOJ49hpLRSyHQL7GDZhp_setting5.PNG)

第四步，到左上方的搜尋框內輸入 `" Source Han Sans Traditional Chinese "` 
![setting6.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/3GZy047zRxiipBu1fDH5_setting6.PNG)
第五步，找到後，我們滑鼠移過去，下滑出下圖的選項，我們點 Use Fonts 
![setting7.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/RCbz1q56QSCueB2lVE0D_setting7.PNG)
第六步，接著我們點 `web` 的頁籤， 依照下圖的順序， 1. 選剛剛自己建立的 kit , 2 按下 `Convert to dynamic  kit `
![setting8.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/uL5HOOgQRSGWUc8uuFJd_setting8.PNG)
第七步，回到了字型細部設定，我自己是會把左手邊的 light 選起來，另外就是保留了 regular , bold ，這樣總共三種粗細可用，然後最後就是按下左下角的 ` publish ` 按鈕。 所有字型細部設定改動過後都需要按 publish 喔!
![setting9.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/DfpdN4GQd6j7ja1mcdow_setting9.PNG)
第八步，點右上角的 ` Embed Code ` 
![setting10.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/zDEfDqpEQqu468jcbxOg_setting10.PNG)
第九步，可以插入的 javascript code 有分為簡單版跟進階版，我自己是直接切換到 show advanced 的頁面，把裡面的程式碼複製下來，接著就可以去 logdown 裡面做設定囉。  
![setting11.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/E3Rz5PX7QpSMQZXh59HK_setting11.PNG)
![setting12.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/6T9Aco6YQEqewKt1F21N_setting12.PNG)


## Logdown的設定
很簡單，我們去客製化我們的CSS檔案即可
![setting13.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/Rkb3coC8QZvx8dfl62IJ_setting13.PNG)

把剛剛 type kit 複製的 javascript 檔案貼到這個位置， 我是貼在 logdown 引入 css檔案後的下一行
![setting14.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/ORrQBmkRHuOIkjYaqY7Q_setting14.PNG)


然後接著在貼上去的 javascript 下面再貼上下面這段 inline css code 。 本來我以為只要貼好 javascript 就好了，但是沒有作用，我只好想到 workaround 用後面覆蓋前面的方式手動補上了。基本上下面這些 tag 應該都涵蓋了所有的文章會用到的 html code, 如果還缺漏的話就自行補上囉。 
![setting15.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/Z47WBXzGRyKdue6qaWxR_setting15.PNG)


```css
 <style type="text/css">
html, body, div, span, applet, object, iframe, h1, h2, h3, h4, h5, h6, p, blockquote, pre, a, abbr, acronym, address, big, cite, code, del, dfn, em, img, ins, kbd, q, s, samp, small, strike, strong, sub, sup, tt, var, b, u, i, center, dl, dt, dd, ol, ul, li, fieldset, form, label, legend, table, caption, tbody, tfoot, thead, tr, th, td, article, aside, canvas, details, embed, figure, figcaption, footer, header, hgroup, menu, nav, output, ruby, section, summary, time, mark, audio, video {
font-family: "source-han-sans-traditional",sans-serif;
font-style: normal;
font-weight: 200;
  }    
 </style>  
```

###於是乎，我們就完成啦~ 有漂亮的字型字體可以看了，手機看也很舒服。
![IMG_4689.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/maoonbWSFe1YxkIhIcMo_IMG_4689.PNG)

## typekit webfont 用量的檢視
我們可以點主頁面的 Account ，然後就可以在左手邊看到已使用了多少
![volume0.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/PbKDXTwoQS20CKgbAwb4_volume0.PNG)
![volume.PNG](http://user-image.logdown.io/user/359/blog/359/post/341822/rTbH3GsQQvqA7dRe5uOH_volume.PNG)
之後再來寫一篇 google blogger 篇。(待續)