---
layout: post
title: "如何使用 mina & puma & nginx 部署 Rails - deploy rails app with mina &amp; puma &amp; nginx "
date: 2017-02-22 17:39:14 +0800
comments: true
categories: rails ruby nginx puma mina deploy devops
---

# 從provision VM 到自動安裝軟體、到使用 mina 自動化部署 Rails App #

##provision 一台新 ubuntu server ##

自動裝機 script , 分兩段:

1- 第一段自動裝軟體 ,
curl -L https://gist.githubusercontent.com/dangjlin/2dd09f80d114e54b54be89ce8b9c6336/raw/d94cfd5eb92905c45899cb41a86c64b9f868efb5/init_install.sh | sh

gist file: (https://gist.github.com/dangjlin/2dd09f80d114e54b54be89ce8b9c6336)[https://gist.github.com/dangjlin/2dd09f80d114e54b54be89ce8b9c6336]

很重要的是：
*裝完之後需手動 source ~/.bashrc 重新載入環境變數*
才可以再繼續run 第二段

2- 一段自動裝 rbenv  & 安裝 ruby 2.3.0
curl -L https://gist.githubusercontent.com/dangjlin/f57d8713352bfc8b0d35c31327658790/raw/efe262d3c030fa32d479b2eb9d852b41edc736d8/rbenv_init.sh | sh

gist file:
(https://gist.github.com/dangjlin/f57d8713352bfc8b0d35c31327658790)[https://gist.github.com/dangjlin/f57d8713352bfc8b0d35c31327658790]

如果VPS都永遠在 AWS 或 Digital Ocean or Linode ,
建議裝好之後自己 create 一個 image file,
以後就用這個image 去 provision new server


##接著我會使用 chef-solo , knife-solo 自動安裝

cookbook 準備了 user , nginx, postgres sql 等自動安裝及自動設定 config template.

補充 knife-solo 的安裝 (摘錄 from Gogojimmy 網頁說明
在我們的本機，我們先安裝好knife，針對我們所使用的是Chef-Solo，因此我們要安裝的是Knife-Solo
`gem install knife-solo`
裝好了knife-solo以後，讓我們使用knife solo init 來開啟一個新專案
`knife solo init knife-solo-demo`
再來我們進行下一步之前我們先產生一個chef的設定檔
`knife configure -r . --defaults`


## 往下設定 config 之前請先完成這幾個動作
1. 把server端的 ~/.ssh/id_rsa.pub 檔案 copy 到本機端的 ~/.ssh/known_host 裡面
2. 把server端的 ~/.ssh/id_rsa.pub 檔案登記到 github 上面，這樣server 端才可以到github拉檔案

## 接著請手動調整下列三個 config file

nginx config file, put it in the site-enable/default  file

```Ruby
upstream app {
    # Path to Puma SOCK file, as defined previously
    server unix:/home/ubuntu/site/app/app-name/current/shared/sockets/puma.sock fail_timeout=0;
}

server {
    listen 80;
    server_name localhost;

    root /home/ubuntu/site/app/app-name/current/public;

    try_files $uri/index.html $uri @app;

    location @app {
        proxy_pass http://app;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
    }

    error_page 500 502 503 504 /500.html;
    client_max_body_size 4G;
    keepalive_timeout 10;
}


```


puma config file in config/puma_production.rb
```Ruby
environment 'production'
threads 2, 64
workers 2   #depends on how many cores of server CPU

application_path = File.expand_path('..', __dir__)
directory application_path

pidfile "#{application_path}/shared/pids/puma.pid"
state_path "#{application_path}/shared/sockets/puma.state"
stdout_redirect "#{application_path}/shared/log/puma.stdout.log", "#{application_path}/shared/log/puma.stderr.log"
bind "unix://#{application_path}/shared/sockets/puma.sock"
activate_control_app "unix://#{application_path}/shared/sockets/pumactl.sock"

daemonize true
preload_app!

```

## Mina Setup 步驟執行之後注意事項
有三件事情要做：
1- 因為有兩個 config 檔案我們是做 symbolic link 到 shared folder  " shared/config " 目錄裡面
所以記得要server 上手動增加   database.yml , secret.yml 檔案
2- 另外還有環境變數也記得手動到server去增加，以利後續 Rails production server 要讀取＆啟動
3- 在 postgresql 手動 create production 的 database.

mina config file in config/deploy.rb
```Ruby
require 'mina/bundler'
require 'mina/rails'
require 'mina/git'
require 'mina/rbenv'
require 'mina/puma'

# Basic settings:
#   domain       - The hostname to SSH to.
#   deploy_to    - Path to deploy into.
#   repository   - Git repo to clone from. (needed by mina/git)
#   branch       - Branch name to deploy. (needed by mina/git)

set :application_name, 'production_app'
set :domain, '192.168.99.99'
set :user, 'ubuntu' # Username in the server to SSH to.
set :deploy_to, "/home/#{fetch(:user)}/site/app/#{fetch(:application_name)}"
set :repository, 'git@github.com:abcdefg/xyz.git'
set :branch, 'master'
set :rbenv_path, "$HOME/.rbenv"
set :database, 'name_of_production'

# shared dirs and files will be symlinked into the app-folder by the 'deploy:link_shared_paths' step.
set :shared_files, fetch(:shared_files, []).push('config/database.yml', 'config/secrets.yml')
set :shared_dirs, fetch(:shared_dirs, []).push('shared/log', 'tmp/cache', 'shared/pids', 'shared/sockets', 'public', 'vendor')

# This task is the environment that is loaded for all remote run commands, such as
# `mina deploy` or `mina rake`.
task :environment do
    invoke :'rbenv:load'
end

task :setup do

end

desc "Deploys the current version to the server."
task :deploy do
  deploy do
    # Put things that will set up an empty directory into a fully set-up
    # instance of your project.
    invoke :'git:clone'
    invoke :'deploy:link_shared_paths'
    invoke :'bundle:install'
    invoke :'rails:db_migrate'
    invoke :'rails:assets_precompile'
    invoke :'deploy:cleanup'

    on :launch do
        in_path(fetch(:current_path)) do
        command %{mkdir -p tmp/}
        command %{touch tmp/restart.txt}
        invoke :'puma_restart'
        end
    end
  end
  # you can use `run :local` to run tasks on local machine before of after the deploy scripts
  # run(:local){ say 'done' }
end

task puma_restart: :environment do
  puma_pid = "#{fetch(:current_path)}/shared/pids/puma.pid"
  command %[
    if [ -e '#{puma_pid}' ]; then
      bundle exec pumactl -F config/puma_production.rb stop
      bundle exec pumactl -F config/puma_production.rb start
    else
      bundle exec pumactl -F config/puma_production.rb start
    fi
  ]
end

# For help in making your deploy script, see the Mina documentation:
#  - https://github.com/mina-deploy/mina/tree/master/docs

```


參考網站：
(http://gogojimmy.net/2013/06/01/Chef-Solo-Basic-with-Vagrant/)[http://gogojimmy.net/2013/06/01/Chef-Solo-Basic-with-Vagrant/]
(https://ruchee.com/notes/2016/deploy_rails_use_ubuntu_nginx_puma_mina.html)[https://ruchee.com/notes/2016/deploy_rails_use_ubuntu_nginx_puma_mina.html]
