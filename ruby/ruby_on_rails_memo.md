## Ruby
### %記法(忘れがち)
参考：https://qiita.com/mogulla3/items/46bb876391be07921743
### CentOSでruby-buildのUpdate(忘れがち)
インストールしようと思ったバージョンがない時
ruby-buildの入っている位置に移動して最新をpull
```
cd /usr/local/rbenv/plugins/ruby-build
git pull origin master
sudo ./install.sh
rbenv install -l
  ...
  ...
rbenv install ○○○
rbenv rehash
```
### 同音文字を数える
String#charsで全ての文字を配列で返し、Array#tallyを組み合わせて一つの文字列に同音文字が何個あるか数えられる
```
irb(main):036> "haruharara".chars
=> ["h", "a", "r", "u", "h", "a", "r", "a", "r", "a"]
irb(main):037> "haruharara".chars.tally
=> {"h"=>2, "a"=>4, "r"=>3, "u"=>1}
```
### 定数の警告
```
再定義→実行できるけど警告
途中で数とか代入する→平気
メソッドの中で定数を定義する→シンタックスエラー
```
### 正規表現での「|」は部分一致になるので気を付ける
```
\A[Ss]ora\z
```
とブラケット([])に特定の文字を記載すればそれのみマッチするが
```
\AS|sora\z
```
とすると、左辺の判定でSに別の文字が続いてもマッチしてしまう(S110等)
### 複数条件がある三項演算子はif~else~end表記にするとわかりやすい
Ruby Silverの問題
```
a = [1, 2, 3, 5, 8]
b = [1, 3, 6, 7, 8]
c = false || true ? true && false ? a | b : a & b : b ;
p c
# 実務でここまで一行にすることはあまりないと思う...
```
一行だと分かり辛いので条件文に直す
```
if false || true #=>true
  if true && false #=>false
    a | b
  else
    a & b
  end
  b
end
```
### 配列の引き算では右辺の値は考慮されない
間違いがち
```
a = [1,2,3,4,5,6]
b = [2,4,6,8]
```
とあってa - bとするとき、bの配列に入っている8は別に結果に入ったりはしない
```
a - b
=> [1, 3, 5]
```
## Rails
### コールバックの順番
実行される順番
before_validation
after_validation
before_save
around_save
before_update　/ before_create
around_update / around_create
after_update / after_create
after_save
after_commit / after_rollback

### Ubuntuを使って動かす際に何故かmysql2のgemインストールだけ失敗する
原因：必要なライブラリがない
結構同じエラーが存在する→https://qiita.com/GandT/items/bee105afe228a564a4da
```
sudo apt install libmysqld-dev
```
```
gem install mysql2
```
大方解決するはず
### cancancan
https://github.com/CanCanCommunity/cancancan
定義の基準
- 現在の操作者が (staff、として)
- どのロールに属しているときに
- どの種類のデータを (テーブル名)
- どういう条件を満たす場合に
- どういう操作ができる（一覧可能、新規登録可能、等）
### ActiveHash
gem
```
gem 'active_hash'
```
複数のタイプがあるようなデータをデータベースに登録しなくても扱える(郵便番号とか都道府県とか)
ActiveHash::Baseクラスを継承したモデルファイルを作成する。
既に存在するテーブルにあるカラムで行いたい場合は、該当のモデルファイルで関連付けする。
(ActiveHash::BaseクラスでApplicationYamlを継承し、config/下にデータ等を記載しても良い)
例
app/model/class.rb
```
# frozen_string_literal: true

class class < ApplicationRecord

  belongs_to :class_type, optional: true, class_name: 'Division::ClassType'
```
app/model/division/class_type.rb
```
# frozen_string_literal: true

module Division
  class ClassType < ApplicationYaml
  end
end
```
config/division/class_type.yml
```
- id: 1
  class_type_id: 1
  name: 花
- id: 2
  class_type_id: 2
  name: 月
- id: 3
  class_type_id: 3
  name: 雪
- id: 4
  class_type_id: 4
  name: 星
- id: 5
  class_type_id: 5
  name: 宙
```

### db/seed.rb等のテストデータで親テーブルに紐づく子テーブルを多言語で複数作成したい
lambda等でブロックにしてから親テーブルをcreateする時に渡して呼び出すとスッキリする

```
# config/initializers/locale.rb
I18n.available_locales = [:en, :ja, :zh-TW]
```
```
 greeting = lambda do
  I18n.available_locales.map do |greet|
    Greet.new(greet:)
  end
end

Human.create!(
  name: '鈴木', live: 'japan', greets: greeting.call
)
```

### FactoryBotで特定の順番のまま値をいれたい
どちらかというとRSpec
```
FactoryBot.define do
  factory :hoge do
    sequence(:color, %w[桃 黄 緑 青 紫 赤].cycle)
  end
end
```
create_list(:hoge)とかすると桃 黄 緑 青 紫 赤の順番で作成される
enumなので文字列に入れたい場合は.next等で表示させる
```
sequence(:favorite, "好きなカラーは(#{%w[桃 黄 緑 青 紫 赤].cycle.next})です")
```
### rails7.1以降はRSpecが例外をキャッチしない
[コントローラーテスト、結合テスト、システムテストの例外処理方法が新しくなった](https://railsguides.jp/upgrading_ruby_on_rails.html#%E3%82%B3%E3%83%B3%E3%83%88%E3%83%AD%E3%83%BC%E3%83%A9%E3%83%BC%E3%83%86%E3%82%B9%E3%83%88%E3%80%81%E7%B5%90%E5%90%88%E3%83%86%E3%82%B9%E3%83%88%E3%80%81%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0%E3%83%86%E3%82%B9%E3%83%88%E3%81%AE%E4%BE%8B%E5%A4%96%E5%87%A6%E7%90%86%E6%96%B9%E6%B3%95%E3%81%8C%E6%96%B0%E3%81%97%E3%81%8F%E3%81%AA%E3%81%A3%E3%81%9F)

`ActiveRecord::RecordNotFound`をキャッチするテストで詰まったのでメモ
```
:rescuable: config.action_dispatch.rescue_responsesで宣言されている例外についてはHTMLエラーページを表示する
Rails 7.1以降で生成したアプリケーションのconfig/environments/test.rbには config.action_dispatch.show_exceptions = :rescuableが設定されます。
```
[ここ](https://railsguides.jp/configuring.html#config-action-dispatch-perform-deep-munge)にある例外が対象

## その他
### centos7でmysql8をインストールができない
これいつ困ったのだろう、でも対処法としてメモしてあったので一旦貼っておく
https://masalib.hatenablog.com/entry/2020/11/12/222647
