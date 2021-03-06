## ファイル名の置換 （renameで正規表現が使えないとき）
[UNIX・Linuxでファイル名・フォルダ名の一括置換を行う | 俺的備忘録 〜なんかいろいろ〜](https://orebibou.com/2015/07/unix%E3%83%BBlinux%E3%81%A7%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E5%90%8D%E3%83%BB%E3%83%95%E3%82%A9%E3%83%AB%E3%83%80%E5%90%8D%E3%81%AE%E4%B8%80%E6%8B%AC%E7%BD%AE%E6%8F%9B%E3%82%92%E8%A1%8C%E3%81%86/#2rename)

## execute background
```sh
$ [command] &

# ssh などでログアウトしても実行させたい場合
$ nohup [command] &
```


## 指定した行と行の間だけ処理
```sh
sed -n '7000000,7030000p' production.log  | grep hogehoge
```


## process all kill
```
# show ps
ps -ef | grep -v grep | grep sidekiq
# kill all
kill -9 `ps -ef | grep sidekiq | grep -v grep |  awk '{print $2;}'`
```


## shell script
[これだけ覚えておけばOK！シェルスクリプトで冪等性を担保するためのTips集 - Qiita](https://qiita.com/yn-misaki/items/3ec0605cba228a7d5c9a)

[変数を使用する | UNIX & Linux コマンド・シェルスクリプト リファレンス](https://shellscript.sunone.me/variable.html)

[シェルで変数のインクリメントに expr を使うと100倍遅い件 - Qiita](https://qiita.com/d_nishiyama85/items/a117d59a663cfcdea5e4)

`$()` と `` はほぼ同じだが、`$()` のほうが高機能


## sed
```sh
grep 'echo $this->element('\'front/common/bread_crumb_list\'');' -n $file | cut -d ":" -f 1

sed -i -E '/^<(ol|ul) class="pankuzu">$/,/^<\/(ol|ul)>$/s/^<li><a href="(.+)" ?>(.+)<\/a><\/li>$/$this->Html->addCrumb('\'\\2\',\ \'\\1\'');/g' $file
```

[ある文字列をファイルの特定行に挿入するコマンド - 元RX-7乗りの適当な日々](http://d.hatena.ne.jp/rx7/20110310/p1)

mac で -i で `invalid command code`
http://arshavin0909.hateblo.jp/entry/2012/08/29/150609

エスケープ
http://akuwano.hatenablog.jp/entry/20120411/1334114116

シングルクォーテーションとダブルクォーテーションのエスケープ
[sedでダブルクォーテーション・シングルクォーテーションの置換・削除を行う | 俺的備忘録 〜なんかいろいろ〜](https://orebibou.com/2016/07/sed%E3%81%A7%E3%83%80%E3%83%96%E3%83%AB%E3%82%AF%E3%82%A9%E3%83%BC%E3%83%86%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%83%BB%E3%82%B7%E3%83%B3%E3%82%B0%E3%83%AB%E3%82%AF%E3%82%A9%E3%83%BC%E3%83%86-2/)

最初にマッチしたものだけ置換
特定の文字に囲まれているものだけ置換
[sedにて一番最初にマッチしたモノだけを置換するには - 計算機と戯れる日々](http://d.hatena.ne.jp/n9d/20081110/1226284188)

-r は -E
http://anton0825.hatenablog.com/entry/20140808/1412911831

いろいろ
[sedでこういう時はどう書く? - Qiita](https://qiita.com/hirohiro77/items/7fe2f68781c41777e507)


## ファイル数カウント
```sh
# -U でソートしない（高速にする）、 -lで一行ずつ表示する

# ディレクトリ含めてカウント
$ ls -U1 | wc -l

# file only
$ ls -F | grep -v / | wc -l
```


## bash で ctrl + s で前方検索できるようにする
通常はctrl+sにスクリーンロックがかかっているため、それを解除する。

.bashrc などに書かないとログイン？の度に設定が無効になる。

```sh
# User specific aliases and functions
if [ "$SSH_TTY" != "" ]; then
    stty stop undef
fi
```

```
$ stty -a
# stop=^S になっていたらスクリーンロック
$ stty stop undef
```

### 参考
[Bashでコマンド履歴から検索して実行する - Qiita](https://qiita.com/quwa/items/3a23c9dbe510e3e0f58e)


# Permission
## chown
```
# 所有者の変更
# chown group file
$ chown mizuno sample.txt

# グループの変更
# chown :group file
$ chown :mizuno sample.txt

# 所有者とグループの変更
# chown user:group file
$ chown mizuno:mizuno sample.txt
```

ユーザー名のあとにコロンを記述しグループ名が無い場合、ファイルのグループはそのユーザのプライマリグループに変更され、ユーザーは指定したものになる

```
# chown user: file
$ chown mizuno: sample.txt
```


- R: 再帰的に変更
- c: 実際に変更があった場合の動作を詳細表示

# 検索
## grep
シングルクォートとダブルクォートはどちらが普通？
変数展開しない限りはシングルクォートでいい？

### 基本
-r で再帰的, -n で行番号出力

```
$ grep search_sring target_file_or_directory
$ grep -rn 'hoge' .
```

### or 検索
```
# -e で繋ぐ (最初につけたオプションはすべて有効になる)
$ grep -e one -e two sample.txt

# 正規表現
$ grep -E 'one|two' sample.txt
```

### and検索
```
# パイプ
$ grep one sample.txt | grep two

# 順番がわかっているなら正規表現とか？
$ grep -E 'one.+two' sample.txt
```

### オプション
- r: ディレクトリを再帰的に検索
- n: 行番号出力
- v: 除外
- c :出現行数カウント
- i: 大文字小文字区別なし
- B n: 前n行
- A n: 後n行
- C n: 前後n行


## find
```
$ find target_directory search_condition action
```

- -name
- -type
  - f: ファイル
  - d: ディレクトリ
  - l: シンボリックリンク
- -iname: 大文字小文字を無視
- -exec: 検索結果に別コマンド実行
- -maxdepth: 検索対象の階層

### sample
#### カレントディレクトリ以下のファイルのパーミッションをすべて600にする
```
$ find . -type f -exec chmod 600 {} +
or
$ find . -type f | xargs chmod 600
```

#### ファイル名でソート

```
$ find . -name なんちゃら | sort
```

#### Permission denied を表示しない
$ find / ... 2>/dev/null

### 参考
[find コマンド | コマンドの使い方(Linux) | hydroculのメモ](https://hydrocul.github.io/wiki/commands/find.html)


## モニタリング

```
watch --interval=秒数 なにか
```

例

```
watch --interval=0.5 'ps aux | grep rails'
```

## du
ファイルやディレクトリの使用容量を集計。
デフォルトはサブディレクトリも表示される。

-c : 検索したすべての総計を表示
- h : 容量を適切な単位で表示
- s : 指定したファイルやディレクトリのみ表示（サブディレクトリは非表示）
- S : 個々のディレクトリでサブディレクトリを含ずに表示


## sed
文字列の置換や行の削除

```
#2~4行目を削除
sed -e "2,5d" test.txt

hogeをfooに置換
sed -e "s/hoge/foo/g" text.txt
```


## シンボリックリンク
```
ln -s リンク元(実体) リンク先(ショートカット)
```
`リンク先 -> リンク元` である。

### 張り替え
```
ln -nfs 新しいリンク元 リンク先
```

### リンク削除
```
unlink リンク元
```

### オプション
- d : ハードリンク
- s : シンボリックリンク

- b : 同名の場合バックアップ作成
- f : フォース、リンク先に同名のファイルやリンクがあっても強制上書き
- i : 上書きされる場合確認
- n : リンク元に既存ディレクトリを指定した場合でも、ディレクトリ中にリンクを作成せず、ディレクトリとリンクを置き換える
- v : 作成状況表示


## less
環境変数LESSにオプション指定で常時有効化

### オプション
- N : 行番号
- g : 検索結果をハイライト
- s : 連続した空白行は一つにまとめる
- p [word] : wordをハイライト
- m : ファイルの進行率だけ表示
- M : もろもろ表示
- S : 折り返さない
- R : カラー
- F : 画面に収まる場合すぐに終了
- i : 検索が全て小文字の場合大文字小文字の区別なし
- X : less終了時にクリアしない
- +F : 最初から監視モード

閲覧中にコマンドをもう一度打つことで有効無効の切り替え可能


### コマンド
vimと同じものが多い？

- f : 1ページ進む
- b : 1ページ戻る
- d : 半ページ進む
- u : 半ページ進む
- gg : ファイルの先頭に移動
- G : ファイルの末尾に移動
- -N<Enter> : 行番号表示
- F : 監視モード( `tail -f` と同じ？)
- =, ^G, :f : ファイル情報表示
- v : エディタ起動
- !<Enter> : シェルに遷移→シェルでexit→lessに戻る
- &<ワード> : 検索（ワードをハイライトする）
- %<パターン> : 正規表現で上方検索
- /<パターン> : 正規表現で下方検索


## scp
### リモートからローカル
```
$ scp ユーザ名@リモートのホスト名:リモートのパス ローカルのコピー先
$ scp user@remoteHost:/home/user/test.txt /local/path
```

### ローカルからリモート
```
$ scp ローカルのパス ユーザ名@リモートのホスト名:パス
$ scp ~/hoge.txt user@remoteHost:~/wp
```


## ls
### ディレクトリだけ
```
ls -l | grep ^d
ls -F | grep /
```

### ファイルだけ
```
ls -l | grep -v ^d
ls -F | grep -v /
```


## 圧縮
```
$ tar cfvz xxxx.tar.gz [target_directory]
$ tar xfvz xxxx.tar.gz
```

- c: 新しいtarファイルを作る (create)
- v: 圧縮・解凍状況を表示(verbose)
- f: 圧縮ファイル名指定 (filename)
- x: 解凍 (extract)
- z: gz
- j: bz2
- J: xz

[[Linux]ファイルの圧縮、解凍方法 - Qiita](https://qiita.com/supersaiakujin/items/c6b54e9add21d375161f)


## ログ監視
lessしてF C-cで戻る /で検索など
tail -Fでも可能

&で検索


### オプション
- s 空行をつなげる
- p 引数の文字ハイライト
- i 検索文字が全部小文字のとき大文字小文字無視
- M プロンプトに詳細表示
- R 色つけ
- S ラップしない

## sudo, su
パスワードはsudoを実行したユーザーのもの

```
$ sudo -u user_name vim sample.txt
```

### ユーザー変更
sudo必要？

```
$ su - user_name
```
