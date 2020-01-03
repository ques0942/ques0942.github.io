---
title: "HugoへのGoogle Analyticsの埋め込み"
date: 2020-01-02T18:25:21+09:00
draft: false
---
Hugoには標準でGoogle Analyticsを組み込む機能があるため、以下の手順で設定すればOK

## まとめ
* `config.toml` に `googleAnalytics` としてトラッキングIDを設定
* テンプレート内に以下のどちらかの手段でトラッキング用のコードを埋め込む
  *  既存のどちらかのテンプレートを使用する
      * `_internal/google_analytics.html` 
      * `_internal/google_analytics_async.html`
  * `.Site.GoogleAnalytics` という変数を使って自力で設定する

## トラッキングIDの設定
config.tomlに以下の内容を追記する

```toml
googleAnalytics = "UA-155249985-1"
```

## トラッキング用のJSの設定
使用しているテーマによっては既に登録されている可能性があるため、
`googleAnalytics` を設定したらまず一度サイトの挙動を確認する。
既にanalytics用のコードが埋め込まれてアクセスが発生しているようなら以降の作業は不要。

テンプレートに以下のどちらかのテンプレートを追加する。

```
{{ template "_internal/google_analytics.html" . }}
```

```
{{ template "_internal/google_analytics_async.html" . }}
```

`.Site.GoogleAnalytics` という変数がconfig.tomlに設定されたトラッキングIDを持っているため、これを使って自力でコードを書いても良い。

