---
title: Ruby on Railsメモ
tags:
  - ruby
  - rails
  - memo
categories:
  - Other
date: 2016-01-12 00:00:00
---

# Ruby on Railsメモ
<!-- more -->

フレームをRuby-on-Railsに選定した．

Rubyのバージョンとgemを管理するため，Pythonのpyenvのように，Rubyにも管理ツールがあるrbenvとrvmがある．ここではrvmを使う．

インストール手順
> $ gpg2 --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3

gpg2がなければ`brew install gpg2`で入れる．

> $ curl -sSL https://get.rvm.io | bash -s stable --rails

Rubyの2.0.0を入れる
> rvm install 2.0.0

使うRubyバージョンを指定
> rvm use 2.0.0

gem環境を作成
> rvm gemset create sandbox

gem環境を指定
> rvm gemset use sandbox

gemでrailsを入れる
> gem install rails

railsでappを生成
> rails new xxx

**A**とという名前の*controller*を作成，**B**と**C**という名前の*view*を作成
> rails g controller A B C

railsのサーバーを`http://localhose:3000`に立つ
> rails s

ルーティング `config/routes.rb`

> get 'users/show' - > get 'users/show/:username' => "users#show"

controllerにも`params[:]`を使って，設定する
`app/controllers/users_controller.rb`

```
def show
  @user = Hash.new
  if params[:username] == 'ryooopan'
    @user[:name] = 'Ryo Suzuki'
    @user[:username] = 'ryooopan'
    @user[:location] = 'Kanagawa, Japan'
    @user[:about] = 'Hello, I am Ryo.'
  elsif params[:username] == 'moyahima'
    @user[:name] = 'Shohei Aoki'
    @user[:username] = 'moyahima'
    @user[:location] = 'Tottori, Japan'
    @user[:about] = 'Nice to meet you.'
  end
end
```

`http://localhose:3000/users/show/ryooopan`にアクセスすると変化が見える

次にデータベースを作成
**veiw,controller <- database <-schema <- migration file**の順に生成する

データベースを生成
> rake db:create

`db/development.sqlite3`が生成される

veiw,controller <- database <-**schema <- migration file**

migration fileとschema生成,
データベースの塊がモデル，Aという名前のモデルを生成
> rails g model A name:string username:string location:string about:text

veiw,controller <- **database <-schema**<- migration file

>rake db:migrate

そうすると，`db/migrate/`にあるmigrateに従ってdatabaseを生成する．`db/seeds.rb`には初期値をいれる

> rake db:seed

で初期値を書き込む
