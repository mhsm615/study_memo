### エイリアス切りたいとき
* いつも覚えられない
```
~/.bash_profile
```
と
```
~/.bashrc
```
両方に記載し、最後に
```
source ~/.bash_profile
```
を行う
### ubuntuでbundle installをすると何故かmysqlだけがはいらないとき
https://qiita.com/GandT/items/bee105afe228a564a4da
ライブラリ不足
### wsl経由でGithubに接続できなくなってしまう
* linux直接ではないけど
仮想環境のDNSアドレスが定まっていなくて稀に起きるらしい、明示的にgoogleのDNSサーバーを使用する
https://qiita.com/kkato233/items/1fc71bde5a6d94f1b982
### 権限確認
* sudoで変更できる

https://qiita.com/shisama/items/5f4c4fa768642aad9e06

| モード(数字) | モード(アルファベット) | 権限 |
| ---- | ---- | ---- |
|4|r|読み取り|
|2|w|書き込み|
|1|x|実行|

上記の合計値を「所有者」「所有グループ」「その他」の順で入力することでパーミッションを変更できる
```
chmod 764 hoge.txt
7→「所有者」に対して「読み取り」「書き込み」「実行」
6→「所有グループ」に対して「読み取り」「書き込み」
4→「その他」に「読み取り」
```

## postgresql
(db関連だけど...)
### PG::ConnectionBad: connection to server at "127.0.0.1", port 5432系エラー
```
postgres psql: error: connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: FATAL:  role "hasumi" does not exist　のエラー
```
困る<br>
まず`psql -l`とかでできなかったら`su postgres`で無理やり`postgres`というルートユーザーに成り代わって入ろう
<br>今お前はルートではない

```
○○@○○○○$ su postgres
Password:
postgres@○○○○$
```
passwordはwsl通したときに自分で設定したやつ<br>
postgresユーザーで入れたら自分を作る
```
postgres@○○○○$ psql
psql (14.12 (Ubuntu 14.12-0ubuntu0.22.04.1))
Type "help" for help.

postgres=# CREATE ROLE 自分の名前 LOGIN CREATEDB CREATEROLE PASSWORD 'hogehoge';
CREATE ROLE
```
己のALTER権限もつけようね
```
postgres=# ALTER ROLE 自分の名前 LOGIN;
ALTER ROLE
```
作ったらできてるか見ようね
```
postgres-# \du
                                   List of roles
 Role name |                         Attributes                         | Member of
-----------+------------------------------------------------------------+-----------
 hasumi    | Create role, Create DB                                     | {}
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
 ```
 己がいれば`\q`で戻って`su　自分の名前`で自分に戻る
 ```
 自分の名前@○○○○$ psql -l
                              List of databases
   Name    |  Owner   | Encoding | Collate |  Ctype  |   Access privileges
-----------+----------+----------+---------+---------+-----------------------
 postgres  | postgres | UTF8     | C.UTF-8 | C.UTF-8 |
 template0 | postgres | UTF8     | C.UTF-8 | C.UTF-8 | =c/postgres          +
           |          |          |         |         | postgres=CTc/postgres
```           

```
$ psql -U hasumi
psql: error: connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: FATAL:  database "自分の名前" does not exist
```
なぜかテーブルもないって言われるので作る
```
postgres=# create database 自分の名前;
CREATE DATABASE
postgres=# \l
                              List of databases
   Name    |  Owner   | Encoding | Collate |  Ctype  |   Access privileges
-----------+----------+----------+---------+---------+-----------------------
 自分の名前 | postgres | UTF8     | C.UTF-8 | C.UTF-8 |
 postgres  | postgres | UTF8     | C.UTF-8 | C.UTF-8 |
 template0 | postgres | UTF8     | C.UTF-8 | C.UTF-8 | =c/postgres          +
           |          |          |         |         | postgres=CTc/postgres
 template1 | postgres | UTF8     | C.UTF-8 | C.UTF-8 | =c/postgres          +
           |          |          |         |         | postgres=CTc/postgres
```        

```
$ bundle exec rails db:create
connection to server at "127.0.0.1", port 5432 failed: fe_sendauth: no password supplied
Couldn't create 'mervis_development' database. Please check your configuration.
bin/rails aborted!
ActiveRecord::ConnectionNotEstablished: connection to server at "127.0.0.1", port 5432 failed: fe_sendauth: no password supplied (ActiveRecord::ConnectionNotEstablished)


Caused by:
PG::ConnectionBad: connection to server at "127.0.0.1", port 5432 failed: fe_sendauth: no password supplied (PG::ConnectionBad)

Tasks: TOP => db:create
(See full trace by running task with --trace)
```
パスワード入れてないよって言われる<br>
`config/database.yml`に`postgres`に設定したやつを書く

```
development:
  <<: *default
  database: ○○○○_development

#password:

  username: 自分の名前
  password: 自分のパスワード
  
$ bundle exec rails db:create
Created database '○○○○_development'
Created database '○○○○_test'
```
できる
