
## What's PWA ?

Progressive Web Apps （プログレッシブ　ウェブ　アップス）の略

Googleが推進している、モバイルユーザーの体験（UX）向上を目的とするプロジェクト・技術のこと

PWAは、わざわざApple StoreやGoogle Play Storeから**インストールする必要はありません**

**不安定なネットワークでも迅速に起動**し、関連性の高い**プッシュ通知を送信**することができます。

**ホーム画面にアイコンを表示**することができ、トップレベルの**全画面表示**で読み込むことができます。

▼このように、Webサイトを直接ホームに追加することでアプリケーション化されます

<img src="https://github.com/umamichi/pwa-sample/blob/master/add-to-home-screen.gif?raw=true">


## 導入事例

大手WEBサイトからで導入がはじまりつつあります

コンバージョンアップにも効果的です

<img src="https://yt3.ggpht.com/a-/AJLlDp2wcXDpKD1W70aErbFQ1LQJzHC2Vbu6HMb6=s900-mo-c-c0xffffffff-rj-k-no" width="100" alt="trivago" /><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/i/iGCN/20170517/20170517170410.png" width="100" alt="Instagram" /><img src="http://masapon03.com/wp/wp-content/uploads/2017/02/twitter-bird-light-bgs.png" width="100" alt="TwitterLight" /><img src="https://www.suumocounter.jp/img/top/illust_suumo_01.png" width="100" alt="suumo" /><img src="https://image.recipe.rakuten.co.jp/d-pc/logo/addtohomescreen_icon_192.png" width="100" alt="楽天レシピ" style="margin-left: 20px" />

+ Trivago
https://www.trivago.com
  
+ Twitter Lite
https://mobile.twitter.com

👉 平均PV65%増、ツイート数75%増🔺

👉 直帰率20%削減⤵︎

+ Instagram
https://www.instagram.com/

+ Wego
https://www.wegotravel.jp/

👉 コンバージョン39%増🔺

+ 楽天レシピ
https://recipe.rakuten.co.jp/

👉 PVが3.1倍に増加🔺

👉 1ヶ月後にプッシュ通知を受け取ったユーザーの訪問数が5.3倍に増加🔺

+ SUUMO
http://suumo.jp

👉 ページの読み込み時間が75%削減⤵︎

+ Alibaba
https://www.alibaba.com

👉 コンバージョン76%増🔺

## Can I use PWA?

Android Chromeはすでに対応済み

iOSは11.3から対応

Edgeもすでに対応

<img src="https://raw.githubusercontent.com/umamichi/pwa-sample/55a18594c274ef7399ea2817a66c06ecaf57b35f/caniuse.png">


## PWA対応させてみる

この3つだけ

1. HTTPS対応
2. manifest.json設置
3. Service Workerを有効にする

### 1. HTTPS対応

SSL証明書の購入が必要で、6千円〜数万円/年程度。

ただし、無料でSSL証明書を発行できる [Let's Encrypt](https://letsencrypt.jp/) というものもあり、

さくらサーバーやロリポップと連携してお手軽にHTTPS化できるようになってきています

今回は、割愛するためにすでにHTTPS化されている、Github Pages（github.io）を使います

Github.com にログインし

`あなたのユーザーネーム.github.io`

という名前のリポジトリを作り、`index.html` を master branch に push しましょう


```html
<!-- index.html -->
<html>
<head>
  <title>github pages test</title>
</head>
<body>
  <h1>github pages test</h1>
</body>
</html>
```

`https://あなたのユーザーネーム.github.io`


にアクセスして、「github pages test」と表示されていたらOKです

**https**でアクセス出来ていることに注目してください


### 2. manifest.json設置

index.htmlと同じ階層に manifest.json を作成しましょう

▼manifest.json

```json
{
  "name": "PWA Sample",
  "short_name": "PWA",
  "background_color": "#fc980c",
  "icons": [{
      "src": "./icon-256.png",
      "sizes": "256x256",
      "type": "image/png"
    },{
      "src": "./icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    }],
  "start_url": "./?utm_source=homescreen",
  "display": "standalone"
}
```

ここで指定するホームスクリーン用アイコンもindex.htmlと同じ階層に配置しましょう

今回はサイズの違う2種類です

[https://umamichi.github.io/pwa-sample/icon-256.png](https://umamichi.github.io/pwa-sample/icon-256.png)

[https://umamichi.github.io/pwa-sample/icon-192.png](https://umamichi.github.io/pwa-sample/icon-192.png)

Googleによると、**192x192のpngアイコン**が登録されていないと、

`ホーム画面に追加`バナーが表示されないので注意しましょう（バナーはAndroidのみ）


▼ 起動時のURLです。PWAとしてアクセスしたことが分かるように、URLにクエリを付与しています

ここで使用している値は、Google Analytics において意味があるというメリットもあります

```
  "start_url": "./?utm_source=homescreen",
```


▼ display タイプを standalone に設定すると、ウェブアプリでブラウザの UI を非表示にすることができます

つまり全画面表示にすることができます

```
  "display": "standalone"
```


### 3. Service Workerを有効にする

Service Workerは、オフライン体験をサポートするために重要です

index.htmlと同じ階層に配置しましょう

```javascript
// service-worker.js
self.addEventListener('install', function(e) {
  console.log('[ServiceWorker] Install');
});

self.addEventListener('activate', function(e) {
  console.log('[ServiceWorker] Activate');
});

// 現状では、この処理を書かないとService Workerが有効と判定されないようです
self.addEventListener('fetch', function(event) {});
```

### 4. index.html から `manifest.json` `service-worker.js` を呼び出す

index.htmlをこのように変更しましょう

さらに、オフラインでアプリケーションが動作することを明確にするために

2種の画像と `p.html` も追加してみましょう


```html
<!-- index.html -->
<html>
<head>
  <!-- manifest.jsonを呼び出しています -->
  <link rel="manifest" href="./manifest.json">
  <script>
    // service workerが有効なら、service-worker.js を登録します
    if ('serviceWorker' in navigator) {
      navigator.serviceWorker.register('./service-worker.js').then(function() { console.log('Service Worker Registered'); });
   }
  </script>
  <title>Hello PWA.</title>
  <style>
    body {
      text-align:center;
      font-size: 40px;
      vertical-align: middle;
    }
    .image {
      width: 80%; 
      margin: auto;
      display: block;
    }
  </style>
</head>
<body>
  <h1>Hello PWA.</h1>
  <img src="./pwa-logo.svg" alt="PWA logo" class="image" />
  <a href="p.html">p.html</a>
</body>
</html>
```

```html
<!-- p.html -->
<html>
<head>
  <title>Hello PWA. p.html</title>
  <style>
    body {
      text-align:center;
      font-size: 40px;
      vertical-align: middle;
    }
    .image {
      width: 80%; 
      margin: auto;
      display: block;
    }
  </style>
</head>
<body>
  <h1>p.html</h1>
  <img src="./p-chan.jpg" alt="p-chan" class="image" />
  <a href="index.html">index.html</a>
</body>
</html>
```

+ 画像1
[https://umamichi.github.io/pwa-sample/pwa-logo.svg](https://umamichi.github.io/pwa-sample/pwa-logo.svg)

+ 画像2 （画像は何でもよいです）
[https://umamichi.github.io/pwa-sample/p-chan.jpg](https://umamichi.github.io/pwa-sample/p-chan.jpg)



すべて、index.htmlと同じ、ルートディレクトリに配置するだけです

あとは、master branch に push しましょう

`https://あなたのユーザーネーム.github.io`

にアクセスして、[このようなページ](https://umamichi.github.io/pwa-sample) になればOKです👌


### 5. PC版Chromeで確認する

PC版Chromeで以下にアクセスし、Developerツールを開きましょう

`https://あなたのユーザーネーム.github.io`

<img src="https://github.com/umamichi/pwa-sample/blob/master/chrome-dev-tool.png?raw=true" alt="PWA check" />


[Application] パネル -> [Manifest] タブから

`Add to homescreen` をクリックしましょう

`chrome://apps` にアクセスしてみてください。Webページが、PWAとして追加されたはずです

Mac Chromeでは下記ディレクトリに `.app` として追加されます

`~/Applications/Chrome Apps.localized/PWA Sample.app` 


### 6. Androidで確認する

Androidでアクセスすると、`ホームスクリーンに追加`を促すポップが出現します

ちなみに、Googleによると、出現には以下の条件があります

**※ 2回以上のアクセスがあり、そのアクセスに 5 分以上の間隔がある。**

ホームスクリーンに追加できたら、起動してみましょう

トップレベルの全画面表示でWebページが表示されたはずです


### 7. iOS SafariでPWAを体験する

Appleによると、iOS11.3からPWAをサポートします

iOSでは`ホームスクリーンに追加`バナーなど、まだ未実装の部分もありますが

このようにPWAとして起動することが確認できます👇


<blockquote class="twitter-tweet" data-lang="ja"><p lang="tl" dir="ltr">PWA in iOS Safari <a href="https://twitter.com/hashtag/PWA?src=hash&amp;ref_src=twsrc%5Etfw">#PWA</a> <a href="https://twitter.com/hashtag/iOS?src=hash&amp;ref_src=twsrc%5Etfw">#iOS</a> <a href="https://twitter.com/hashtag/Safari?src=hash&amp;ref_src=twsrc%5Etfw">#Safari</a> <a href="https://t.co/doNydI7Bw5">pic.twitter.com/doNydI7Bw5</a></p>&mdash; umauma (@umamichim) <a href="https://twitter.com/umamichim/status/976076275579109377?ref_src=twsrc%5Etfw">2018年3月20日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


機内モード、つまりオフラインでも動作していることに注目してください


## PWAとネイティブアプリの比較

| | PWA | ネイティブアプリ |
| -------- | -------- | ----- |
| 速度 | 遅め | 早い | 
| インストール | ブラウザからワンタップ | Storeから検索が必要 | 
| 開発コスト | 低 | 高 | 
| リリースのタイミング | developerの任意のタイミング | Store申請＆ユーザーが手動で行う | 
| オフラインで動作 | ○ | ○ | 
| ハードウェアアクセス | △ | ○ | 
| Push通知 | ○（Androidのみ） | ○ | 
| iOS, Android | ワンソースで完結 | OSごとに開発が必要 | 
| 全画面 | ○ | ○ | 


PWAの大きなメリットの一つとして、インストールとStore申請の手間が省かれることがあります

また、1ソースで複数のプラットフォームに対応でき、開発コストが少なくて済みます

リリースのタイミングも開発者の任意のタイミングで良いです

ユーザーはわざわざStoreからアップデートをする必要がありません


## ⚡️iOS PWA vs Android PWA vs Native⚡️

| | iOS PWA | Android PWA | ネイティブ |
| -------- | -------- | ----- | ----- |
| Push通知 | × | ○ | ○ |
| Web App Banner（インストールを促すバナー） | × | ○ | - |
| 場所情報 | ○ | ○ | ○ |
| センサー情報（磁力、加速度、ジャイロスコープ） | ○ | ○ | ○ |
| スピーチ（読み上げ） | ○ | ? | ○ |
| カメラ | ○ | ○ | ○ |
| Payment Request APIを使ってApplePayの起動 | ○ | - | ○ |
| オフライン時のデータ容量を50MB以上保持できるか | × | ○ | ○ |
| ユーザーがアプリを数週間以上使わなくてもデータ保持する | × | ○ | ○ |
| Bluetoothアクセス | × | ○ | ○ |
| 音声認識との連携 | × | ○ | ○ |
| 画面回転のロック | × | ○ | ○ |
| シリアル, ビーコン, TouchID, Face ID, ARKit, 高度計, バッテリー情報 | × | ? | ○ |
| Splash screen（PWA起動画面）のカスマイズ | × | ○ | - |
| インストール時にアイコンの名前を変えることができる | ○ | × | - |
| アプリ内課金 | × | ○ | ○ |
| 連絡先やバックグラウンド位置情報などのプライベートなデータへのアクセス | × | × | ○ |
| アイコンのバッジ（未読があるときにアイコン右上に表示される数字） | × | ? | ○ |
| 画面左からスワイプで戻る | × | 未調査 | ○ |

注目すべき点として

+ Push通知とWeb App BannerはiOS PWAでは未実装

+ Android PWAの方ができることがかなり多く、iOSと差異がある

+ ハードアクセス, Push通知を必要としないアプリであればiOSでもPWAで実現可能

+ iOS PWAでは、戻るボタンをつけないとスワイプでは戻れない

## まとめ＆今後

+ 徐々にネイティブアプリからPWAへの移行が始まる

+ ユーザーにとっても、PWA体験はネイティブアプリより好まれる

+ ゲームなどの高度なグラフィック処理・大量の計算処理、ネイティブでしか実現できないのみ、ネイティブアプリとして存続


## （余談1）metaタグ "apple-mobile-web-app-capable" との違いは？

iOS Safariではmetaタグに以下を追加することによって、

ホーム画面から起動したときに全画面表示にして、ネイティブアプリのように見せかけることができる


```
<meta name="apple-mobile-web-app-capable" content="yes">
```

さらに以下を追加することで、 ステータスバーの色も変えられる

```
<meta name="apple-mobile-web-app-status-bar-style" content="black">
```

<img src="https://github.com/umamichi/pwa-sample/blob/master/apple-mobile-web-app-status-bar-style.png?raw=true">


これとPWAの違いは何か？


| | iOS PWA | apple-mobile-web-app-capable |
| -------- | -------- | ----- |
| オフラインで動作 | ○ | × | 
| 全画面 | ○ | ○ | 

オフラインで動作できるかどうか、が違います

また、このmetaタグはAndroidでは有効になりません

PWAでこのタグを使うことでiOSのステータスバーをカスタマイズすることが可能です


## （余談2）そもそもホーム画面に追加する必要があるのか？

最近のiOSではホーム画面にアイコンがなくても、ネイティブアプリやウェブサイトにすぐにアクセスできるようになってきています（Siriからの提案等）

すぐにアクセス出来てしまえば、ホーム画面に追加しようが、ネイティブであろうが、ブラウザであろうが、ユーザーにとっては関係がないことです

つまりPWAにおける「ホーム画面に追加できる」 という機能自体は**あくまで付加価値**であって、

最終的にはブラウザですべて実現できるWEBアプリケーション、に向かっていくのでは？（予想）


## 参考

https://qiita.com/j16a/items/d3f7f7c3bb283bb20f23

https://developers.google.com/web/fundamentals/app-install-banners/?hl=ja

https://qiita.com/moroya/items/0dfab2aff52d3373ce39

https://medium.com/@takeshiamano/ios%E3%81%AE11-3%E3%81%8B%E3%82%89%E3%81%AEpwa%E5%AF%BE%E5%BF%9C%E3%81%A7%E3%81%A7%E3%81%8D%E3%82%8B%E3%82%88%E3%81%86%E3%81%AB%E3%81%AA%E3%81%A3%E3%81%9F%E3%81%93%E3%81%A8-313f638a172b