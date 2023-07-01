---
title: "MacにJMeterをインストール" # 記事のタイトル
emoji: "🧠" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["React", "svg"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
date: "2023.06.17"
---

## 最初に

新機能開発で JMeter を使ってテストする必要がある箇所があったので、インテル Mac にインストールしました。
その手順を記事にしたいと思います。

## 前提条件

- Mac インテルチップである。（たぶん M1 でも同じ手順だと思うが一応）
- Homebrew がインストール済み

## Java をインストール

JMeter は Java がインストールされてないと起動できないためインストールしていく。

```zsh
# Javaのインストール
brew install openjdk

# シンボルを作成、これでJava ~でコマンド使用出来るようになる
sudo ln -sfn $(brew --prefix)/opt/openjdk/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk.jdk

# 作成したシンボルを削除したい時（※実行して確認を取ってないので、こちらのコマンドは実行前に確認をお願いします。）
sudo rm /Library/Java/JavaVirtualMachines/openjdk.jdk

# Javaのバージョン確認
# 問題なくインストールされていればバージョンが表示される
java -version
```

### openjdk とは

なんで Java という名前じゃないかと言うと開発用の Java Development Kit を略したものためです。
Java の実行環境以外に Java の開発環境も付属されているためこの呼び方になっています。
なのでもし今回みたいに Java で作られたアプリを動かすために Java が必要な場合は、JRE（Java Runtime Environment）のインストールで十分そうです。
ただ Java でのプログラム開発もするかも知れないので今回は jdk をインストールします。
※JDK に JRE が含まれているようなイメージです。
[【プログラミング入門】Java インストール方法 [2023 年最新] [初心者：第 1 回] Java プログラムの始め方、環境構築](https://www.youtube.com/watch?v=LS0o7mKg_Qg)

そして jdk にも色々種類があって様々な企業が jdk を提供しています。
昔色々あって、openjdk を元に派生した jdk 様々な企業が提供しているらしい。
なので基本的にはそれらの元となっている。openjdk をインストールしておけば良さそうです。

詳しく背景を知りたい人はここに書いてある。
[OpenJDK（Open Java Development Kit） どれを使ったら良い？](https://ts0818.hatenablog.com/entry/2019/07/27/152156)

## JMeter をインストール

![JMeterダウンロード](https://github.com/wimpykid719/react-component/assets/23703281/dfc03f9c-b377-404c-b821-7e367f998c6b)

[ここから](https://jmeter.apache.org/download_jmeter.cgi) バイナリファイルをダウンロードする。

ダウンロードできたら、セキュリティーのためにダウンロードファイルが途中で改竄されていないか、署名を確認する。

```zsh
# ダウンロードしたJMeterファイルの署名確認
shasum -a 512 apache-jmeter-5.5.zip

# 実行すると下記のような文字列が出力される。これをダウンロードボタンの横にあった　sha512　をクリック遷移したページと比較する。
# 全く同じだった場合は通信経路の途中で改竄されてなかったことになる。
b24cdaa57234153df34a40bdc4501aa16f3286ca3e172eb889a5daa0ded86ab51388af1ea56e756df566a6f74f39f80eceb04e5d559668aeac9ec9759d6445ac  apache-jmeter-5.5.zip
```

署名の確認も済んだら、解凍して解凍したフォルダ内の`bin/jmeter` というファイルをクリックすると JMeter が起動する。

![JMeter起動](https://github.com/wimpykid719/react-component/assets/23703281/2af07b68-ecc2-42ec-9b08-bce8dbe41879)

お疲れ様でした 🎉

## 最後に

JMeter のテストケースまでは作成して、実際に新規開発した機能のテストを実施はしていないのでテストを実施したらまたその時の内容を記事に出来たらと思います。読んで頂きありがとうございました。

### 参考文献

[mac に homebrew で openjdk を入れる](https://zenn.dev/roronya/articles/20230213184800)

🕊：[Twitter](https://twitter.com/Unemployed_jp)
📺：[Youtube](https://www.youtube.com/channel/UCT3wLdiZS3Gos87f9fu4EOQ/featured?view_as=subscriber)
👨🏻‍💻：[Github](https://github.com/wimpykid719?tab=repositories)
😥：[Stackoverflow](https://ja.stackoverflow.com/users/edit/22565)
