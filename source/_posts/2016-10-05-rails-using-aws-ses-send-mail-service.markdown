---
layout: post
title: "Rails using AWS SES send mail service"
date: 2016-10-05 17:24:59 +0800
comments: true
categories: Rails
---
#Ruby on Rails 如何使用AWS SES 的寄送MAIL服務
網站可以 send mail 了 , 感謝 AWS SES service.

有幾個小坑要注意一下：

1. 使用 devise gem 時， initializers 目錄裡面的 devise.rb 這一行請不要亂打，AWS會驗證是不是已 verified domain , 否則會拒絕,不讓你寄信．
config.mailer_sender = 'service@aaa.bbb.ccc' 

2. username & password 要在 SES 的管理頁面 「SMTP Settings」那邊有一個 create SMTP credentials ，才可以產生 SMTP 的 user & password ,  不是自己跑去 IAM 生一組。

3. 測試的時候，請先在SES 管理頁面，左邊有一個 「Email Addresses」先把要測試的收信的 mail address 打進去並且經過驗證，否則填入未經驗證的收信信箱，測試的時候SES也不讓你寄。

4. 其餘的話就是在 environment/production.rb 把下面內容設定好

  config.action_mailer.delivery_method = :smtp
  config.action_mailer.default_url_options = { :host => 'http://aaa.bbb.ccc/'}

  config.action_mailer.smtp_settings = {
    :address => 'email-smtp.us-west-2.amazonaws.com',
    :port => 465,
    :authentication => :login,
    :user_name => ENV['AWS_SES_USER'],
    :password => ENV['AWS_SES_PASS'],
    :domain => "aaa.bbb.ccc",
    :ssl => true,
    :tls => true,
    :enable_starttls_auto => true
}

5. 確定沒問題之後，要離開 sandbox 到正式環境使用時，就在SES的介面填申請表，AWS會審核開通。
