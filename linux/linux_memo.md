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
