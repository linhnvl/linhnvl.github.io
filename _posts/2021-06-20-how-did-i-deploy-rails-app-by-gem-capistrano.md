---
layout: post
title: Tôi đã deploy Rails bằng Gem Capistrano như thế nào?
image: /img/2021-06-20/capistrano.png
---


**Tạo Server và cài đặt môi trường**

Bước đầu tiên, chúng ta cần tạo 1 con server để có thể deploy code lên trên đó. Có nhiều cách như mua VPS ở trên AWS, GCP hoặc tạo một server ảo bằng docker. Ở đây mình chọn cách mua một con server Ubuntu ở trang Vultr.com
![Vultr.com](/img/2021-06-20/vps.png)

Sau đó ssh vào con server và cài đặt môi trường Ruby On Rails theo hướng dẫn ở đây:
[Install RoR](https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-with-rbenv-on-ubuntu-18-04)

**Tạo repo và config cho Capistrano**

Ta sẽ tạo 1 repo Rails hoặc dùng repo có sẵn rồi thêm những dòng này vào Gemfile
```
  gem "capistrano"
  gem "capistrano3-puma"
  gem "capistrano-rails", require: false
  gem "capistrano-bundler", require: false
  gem "capistrano-rbenv"
  gem "capistrano-unicorn-nginx"
```
`bundle install`

`bundle exec cap install` để generate ra các file config theo cấu trúc
```
├── Capfile # Đây là file require các thư viện liên quan
├── config
│   ├── deploy
│   │   ├── production.rb # File config deploy cho môi trường production
│   │   └── staging.rb # File config deploy cho môi trường staging
│   └── deploy.rb # File config deploy chung
└── lib
    └── capistrano
            └── tasks
```
***Thêm các dòng require vào file Capfile***
```
# Capfile
require "capistrano/setup"
require "capistrano/deploy"
require "capistrano/scm/git"
install_plugin Capistrano::SCM::Git
require "capistrano/rbenv"
require "capistrano/bundler"
require "capistrano/unicorn_nginx"
require "capistrano/rails/migrations"
require 'capistrano/puma'
install_plugin Capistrano::Puma

Dir.glob("lib/capistrano/tasks/*.rake").each { |r| import r }
```
***Thêm các dòng config vào file config/deploy.rb***
```
lock "~> 3.16.0"

set :application, "shoppy"
set :repo_url, "git@github.com:linhnvl/shoppy.git"
server "my_ip", user: "deploy", port: 22, roles: [:web, :app, :db], primary: true
set :user, "deploy"
set :branch, :master
set :stage, :production
set :rails_env, :production
set :deploy_to, "~/shoppy"
set :keep_releases, 5
set :linked_files, %w{.env}
set :linked_dirs, %w{log tmp/pids tmp/cache tmp/sockets vendor/bundle public/upload node_modules}
```
Ta cũng có thể config theo từng môi trường staging hoặc production
```
# config/deploy/production.rb or config/deploy/staging.rb
set :user, "deploy"
set :server_name, "my_ip"
set :branch, 'master'
set :rails_env, 'production'
set :bundle_flags, "--no-deployment"

role :app, ["#{fetch(deploy_user)}@#{fetch(server_name)}"]
role :web, ["#{fetch(deploy_user)}@#{fetch(server_name)}"]
role :db,  ["#{fetch(deploy_user)}@#{fetch(server_name)}"]

server fetch(server_name), user: fetch(deploy_user), roles: %w{web app db}, primary: true
```

**Deploy**

Vậy là xong quá trình config, bây giờ chỉ cần chạy `cap production deploy` để tự động deploy trên server



Happy coding !!!
