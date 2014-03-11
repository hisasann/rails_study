# Ruby on Rails チュートリアル：サンプルアプリケーション

これは以下で使用する最初のアプリケーションです。
[*Ruby on Rails Tutorial: Learn Rails by Example*](http://railstutorial.jp/)
by [Michael Hartl](http://michaelhartl.com/).


初めてのデプロイ後に

The page you were looking for doesn’t exist.

が出てしまい、以下を参考にしたがとくに解決しなかったので、スキップ

http://qiita.com/seltzer/items/d52a31c558a30c857a3f



Users、Micropostsをscaffoldしたあとに、

/usersにアクセスしたが、

We're sorry, but something went wrong.

が出てしまった。

heroku run rake db:migrate

をやってみたら、

─[~/Dropbox/code3/rails_study]─[hisamatsu@LAB-N494]─[0]─[10079]
╰─[:)] % heroku run rake db:migrate                                              [master ~/Dropbox/code3/rails_study]
Running `rake db:migrate` attached to terminal... up, run.4415
 !
 !    Timeout awaiting process

 こうなってしまいtimeout

http://stackoverflow.com/questions/21008645/heroku-crashing-on-rake-dbmigrate
を見て
heroku run:detached rake db:migrate
をしたらうまくいった！



bundle exec rspec spec/requests/static_pages_spec.rb

を実行したら、

Migrations are pending; run 'bin/rake db:migrate RAILS_ENV=test' to resolve this issue. (ActiveRecord::PendingMigrationError)

と出た。

http://masoojp.blogspot.jp/2014/01/bundle-exec-rspec.html
を参考にして、どうやらtest用のデータベースが必要みたい。

確かに↑のエラーにもそう書いてある。

rake db:migrate RAILS_ENV=test

したら、rspec実行できた。

と思ったらできていなくて、

Failure/Error: visit '/static_pages/home'
     NoMethodError:
       undefined method `visit' for #<RSpec::Core::ExampleGroup::Nested_1::Nested_1:0x007ffa6546a070>
     # ./spec/requests/static_pages_spec.rb:6:in `block (3 levels) in <top (required)>'

CapybaraのDSLが読み込めていないよう。

http://stackoverflow.com/questions/8862967/visit-method-not-found-in-my-rspec
なので、spec_helper.rbに

config.include Capybara::DSL

を追記。




titleタグの中身のテストを含む状態で、

bundle exec rspec spec/requests/static_pages_spec.rb

がいっこうに成功しなくて、どうもググるとCapybaraが2.1からいろいろ変わったみたい。

Capybara.configure do |config|
  config.match = :one
  config.exact_options = false
  config.ignore_hidden_elements = false
  config.visible_text_only = true
end

をspec_helper.rbに追記したらうまくいった。