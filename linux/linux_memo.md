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
