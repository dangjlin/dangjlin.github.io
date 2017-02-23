---
layout: post
title: 'bitnami wordpress apache subdomain virtual host 設定'
date: 2016-07-05 07:04
comments: true
categories: 
---
# bitnami wordpress apache subdomain & virtual host 設定
因為最近要把一個本來住在別的地方還是以 windows server 為 base 的 wordpress 搬家到 AWS 
為了省去還要自己安裝 php , mysql , wordpress , 搞各種設定檔很麻煩的過繁瑣事務
看到 AWS 上面有 bitnami 出的 AMI 檔裡面已經有了基本的 LAMP + Wordpress. 
很開心的就用了，結果是個小惡夢

對熟悉的人來說可能還好，但我太久沒有設定 apache 了，明明印象當中 subdomain 就是放到 virtual host 檔案裡面指定好目錄就好啦，但因為這個 AMI 是 Bitnami 綁了 wordpress 包出來的，他把 apache 的設定檔變成了一層包一層


然後又因為我是綁了 wordpress 的關係，現在有幾個設定檔可以設定呢？

/opt/bitnami/apache2/conf/httpd.conf
/opt/bitnami/apache2/conf/bitnami/bitnami.conf
/opt/bitnami/apache2/conf/bitnami/httpd.conf  (這個這次不用改 可以忽視他）
/opt/bitnami/apache2/conf/bitnami/bitnami-apps-prefix.conf 
/opt/bitnami/apache2/conf/bitnami/bitnami-apps-vhosts.conf

你以為這樣就沒了嗎？在 bitnami 所謂的 apps 裡面還有 apache 要讀的設定檔勒
/opt/bitnami/apps/wordpress/conf/httpd-app.conf
/opt/bitnami/apps/wordpress/conf/httpd-prefix.conf
/opt/bitnami/apps/wordpress/conf/httpd-vhosts.conf

我的情境是這樣
我的主網站放置的地方是  www.example.com
/opt/bitnami/apps/wordpress/htdocs/MAINSITE
副站放置的地方是
/opt/bitnami/apps/wordpress/htdocs/OTHER-A
/opt/bitnami/apps/wordpress/htdocs/OTHER-B

所以怎麼做？ 
## bitnami apache wordpress virtual host 修改步驟
1.打開 `/opt/bitnami/apache2/conf/bitnami/bitnami.conf` 把下面這行註解掉
```
# Include "/opt/bitnami/apps/wordpress/conf/httpd-prefix.conf"
```
2.apache 本來會把主站的根目錄指去這裡 
  /opt/bitnami/apps/wordpress/htdocs/
  因為我們註解掉了之後， 他所謂的 prefix 模式就沒了，所以他變成去讀這個檔案
  /opt/bitnami/apache2/conf/bitnami/bitnami.conf
  裡面寫的根目錄路徑
  /opt/bitnami/apache2/htdocs
  所以我要把他換掉
  換成
  /opt/bitnami/apps/wordpress/htdocs/MAINSITE

3.然後去打開這個檔案 `/opt/bitnami/apache2/conf/bitnami/bitnami-apps-vhosts.conf`
加上下面這一行
```
 Include "/opt/bitnami/apps/wordpress/conf/httpd-vhosts.conf"
```

4.最後，就是我們要編輯 subdomain 副站的目的地了
打開 ` /opt/bitnami/apps/wordpress/conf/httpd-vhosts.conf `
把 subdomain 放進去。
```
<VirtualHost *:80>
  ServerName Other-A.example.com
  DocumentRoot "/opt/bitnami/apps/wordpress/htdocs/OTHER-A"
  <Directory "/opt/bitnami/apps/wordpress/htdocs/OTHER-A">
    Options FollowSymLinks MultiViews
    LanguagePriority en
    ForceLanguagePriority Prefer Fallback

    AllowOverride All
    <IfVersion < 2.3 >
      Order allow,deny
      Allow from all
    </IfVersion>
    <IfVersion >= 2.3 >
      Require all granted
    </IfVersion>
  </Directory>
</VirtualHost>
```
Other B 一樣啦，就不寫了。
另外 DNS 那邊也要把 subdomain 的 cname給設定好喔！

以下是官網上的wiki document 
>
How To Create A Virtual Host?
Using a Virtual Host allows you to access an application at (for example) http://SERVER-IP/ or http://APPNAME.SERVER-IP instead of http://SERVER-IP/APPNAME.
>
This example shows how to configure WordPress to be accessible from http://wordpress.example.com. Follow these steps:
>
Comment out the line that includes the prefix configuration file in the /opt/bitnami/apache2/conf/bitnami/bitnami-apps-prefix.conf file:
>
 # Include "/opt/bitnami/apps/wordpress/conf/httpd-prefix.conf"
Include the virtual host configuration file for WordPress in the /opt/bitnami/apache2/conf/bitnami/bitnami-apps-vhosts.conf file:
>
 Include "/opt/bitnami/apps/wordpress/conf/httpd-vhosts.conf"
Update the URL in the application if necessary.
>
Restart the Apache server:
>
 sudo /opt/bitnami/ctlscript.sh restart apache