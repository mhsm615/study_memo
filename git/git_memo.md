### 直前のcommitを取り消す
```
git reset --soft HEAD^
```
### git logでファイル名確認
```
git log -p --name-only
```
### 開いていたファイルを作成(編集)したユーザーを見る
```
git log !$
```
### 派生元を後から変更する
```
git rebase --onto (本来派生させるべきのbranch) (今派生しているbranch) (今派生元を変えようとしている自分のいるbranch)
```
### ファイル同士の差分を見る
```
git diff -y ○○.text ○○.text
```
### diffの文字化けを日本語に戻す
```
git config --global core.pager "LESSCHARSET=utf-8 less"
```
