---
title: "JMeterを使う" # 記事のタイトル
emoji: "🧠" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["React", "svg"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
date: "2023.06.17"
---

## 最初に

以前に[Mac に負荷テストツール JMeter をインストールする方法]()という記事で JMeter をインストールしました。
今回は実際にテストケースを作成して負荷テストをどのように行なったのか紹介できればと思います。

## 前提条件

- JMeter がインストール済み

## テストケース作成

![JMeter起動](https://github.com/wimpykid719/react-component/assets/23703281/2af07b68-ecc2-42ec-9b08-bce8dbe41879)

前回の起動したところから始めていきます。

![スレッドループの追加](https://github.com/wimpykid719/company-blog/assets/23703281/4193e266-2b62-4d12-9ffb-3dfe61699b48)

Test Plan を右クリックして `Add ＞ Threads（Users） ＞ Thread Group` をクリック

Thread Group が追加されると思うので設定をしていきます。

![Thread Groupの設定](https://github.com/wimpykid719/company-blog/assets/23703281/e6291cec-b3fb-47a4-9d19-1dc3c1d2b45d)

ここで設定するのは赤枠で囲った 3 つの箇所

- Number of Threads（users）:　非同期でリクエスト投げる数
- Ramp-up period（seconds）：　どれくらいの時間までに指定のリクエストを投げるのか
- Loop Count: 1 スレッドが繰り返して投げるリクエストの数

なので例えば

- Number of Threads（users）:　 30
- Ramp-up period（seconds）：　 300
- Loop Count: 300

とすると 5 分間で 30 x 300 = 9000 リクエストを投げるという設定になります。
ここはどれくらいの負荷でテストしたいかに合わせて変更してください。
1 日のアクセス数とかが参考になるかと思います。

ただここで大量のスレッドを 1 台の PC で立てすぎてしまうと場合によってはフリーズする可能性があるそうです。
回避策として同じ画面内の `Delay Thread creation until needed` にチェックをしておくと良いそうです。

続いて実際に負荷をかけるページの設定を行いたいと思います。

![HTTPリクエストを作成](https://github.com/wimpykid719/company-blog/assets/23703281/80809a90-604b-4d77-982e-608bc6e180b2)

Thread Group を右クリックして `Add ＞ Sampler ＞ HTTP Request` をクリック

![HTTPリクエスト設定](https://github.com/wimpykid719/company-blog/assets/23703281/62eff380-93e4-4987-8d9f-89197e1ea6ec)

ここで設定するのは主に 5 つとなる

- Protocol [http]
- Server Name or IP
- HTTP Request
  - Methods
  - Path
- Parameters

Google 検索のリクエストを例に作成してみようと思います。

各値を埋めていくために自分ががよくやる方法を紹介したいと思います。

まず google chrome の検証ツールを使います。 [https://www.google.co.jp/](https://www.google.co.jp/) に移動します。
今回は参考に google のページで行なっていますが、任意の負荷をかけたいページで行なってください。

**この JavaScript を無効にする操作は基本的に不要**

検証ツールを開いてまずページ上の JavaScript を無効にします。（基本的なサービスでこの操作は不要）
※google 検索特殊で JavaScript 有効状態での検索した際リクエストメソッドが表示されない通信が発生していました。分かりにくかったので無効にして通常のリクエストメソッドでやり取りして頂きます。
検証ツールから JavaScript の無効はこの記事が参考になります。
[chrome の javascript を一時的に無効化](https://www.nextdoorwith.info/wp/se/chrome-disabling-javascript/)

あとは検証ツールの Network タブを開いた状態で任意の検索ワードを入力して検索します。

![検証ツールからリクエスト情報収集](https://github.com/wimpykid719/company-blog/assets/23703281/40c802ac-3f7c-4505-829b-1311c18ab623)

すると負荷テストを行いたいページのリクエストの情報が表示されるので入力していきます。

- Protocol [http]: https
- Server Name or IP: www.google.co.jp
- HTTP Request
  - Methods: GET
  - Path: search
- Parameters: q=test&gbv=1&sei=e3OeZPXIO_yd0-kPlJG_mAQ

※パラメータはコピーして `Add from Clipboard` を押すと Name, Value をいい感じで入力してくれます。

必要な値を入力するとこんな感じになります。

![google検索負荷テスト](https://github.com/wimpykid719/company-blog/assets/23703281/4f07b8f6-901d-4c25-b06f-369a9fab2977)

サイトによってはリクエストヘッダーが必要な場合があります。
その際は Test Plan を右クリックして `Add ＞ Config Element ＞ HTTP Header Manager` をクリックすると設定が追加されます。あとは HTTP Request の設定と同様に検証ツールに表示される値を入力してください。
今回は不要なため省略します。

## 負荷テストの結果を表示

Thread Group を右クリック `Add ＞ Listner ＞ View Results Tree` をクリック

![負荷テスト実行結果を表示設定](https://github.com/wimpykid719/company-blog/assets/23703281/59ce9ad2-7070-4e62-8866-c8de5feeee82)

これで先ほど作成した負荷テストを実行した際にここに実行結果が表示されるようになりました。

負荷テストの実行は左上緑の三角アイコン（Start）をクリック

今回は 5 回リクエストを投げるように設定しました。
リクエストは 200 が返り成功のように思えますが、
`Response Body` を確認して期待した値が返る事を確認します。
そうでないとぱっと見はリクエスト成功に見えてもリクエストヘッダーやパラメータが不足していて期待したリクエストが投げられておらず、負荷テストとして測れてない場合があります。
![負荷テスト実行結果](https://github.com/wimpykid719/company-blog/assets/23703281/bad6fafb-a988-4f22-b73d-36e39376e818)

これで負荷テストが動作して結果も返ることが確認出来ました。

## CUI モードで負荷テストを実行

テストを実際に実行して結果を表示するところまで行いました。
ただ本番の負荷テストでは Apatch は GUI モードでのテストを推奨してないそうです。

なので先ほど作成したテストを保存して `作成したテスト.jmx` ファイルを用意します。

そしてコマンドラインから実行します。
※パスは jmeter をダウンロードしたパスに合わせて変更してください。自身の環境ではデスクトップに置いてあるので下記のパスになっています。

- `-n`: これは非 GUI モードを指定します。
- `-t`: これは実行するテスト計画の JMX ファイルへのパスを指定します。
- `-l`: これは結果を保存する場所を指定します。この場合、結果は JTL 形式で保存されます。

```zsh
~/Desktop/apache-jmeter-5.5/bin/jmeter -n -t ~/Desktop/apache-jmeter-5.5/bin/templates/google-search.jmx -l ~/Desktop/results.jtl
```

実行すると `results.jtl` ファイルが指定した箇所に生成される。結果を確認するには JMeter の `View Results Tree` の `Browse` で生成されたファイルを開くことで結果を確認出来ます。

![CUIで実行した結果を見る](https://github.com/wimpykid719/company-blog/assets/23703281/a04c8967-99fa-4397-82f0-27cad86ca73e)

CUI で出力された結果には `Response Body` にデータ等が記載されていないので注意が必要です。これは実行時の負荷を軽減するために意図的に保存しないようになっていると思われます。
なのでデフォルトではそれらの結果を保存しないようになっています。
保存して確認したい場合は下記のであらかじめ有効にしておく必要があります。 apache-jmeter-5.5 ＞ bin フォルダ内の `jmeter.properties` という設定ファイルの下記の欄を `true` に変更することで保存されるようになります。

```txt
jmeter.save.saveservice.requestHeaders=true
jmeter.save.saveservice.responseHeaders=true
jmeter.save.saveservice.responseData=true
```

## 最後に

これで JMeter のインストールから負荷テスト実施まで一通り行う事が出来ました。実際の負荷テストでは上記の作成方法に従って、5 分間で 9000 リクエストを投げて特に問題なくレスポンスが返ってきているのを確認出来たのでよかったです。最後まで読んでいただきましてありがとうございました。

### 参考文献

[【図解】はじめてでもわかる JMeter の使い方](https://tech-blog.rakus.co.jp/entry/2017/08/24/111332)

🕊：[Twitter](https://twitter.com/Unemployed_jp)
📺：[Youtube](https://www.youtube.com/channel/UCT3wLdiZS3Gos87f9fu4EOQ/featured?view_as=subscriber)
👨🏻‍💻：[Github](https://github.com/wimpykid719?tab=repositories)
😥：[Stackoverflow](https://ja.stackoverflow.com/users/edit/22565)
