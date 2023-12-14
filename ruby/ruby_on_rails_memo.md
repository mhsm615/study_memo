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
  name: '鈴木', live: 'japan', licenses: build_licenses.call
)
```

## その他
### centos7でmysql8をインストールができない
これいつ困ったのだろう、でも対処法としてメモしてあったので一旦貼っておく
https://masalib.hatenablog.com/entry/2020/11/12/222647
