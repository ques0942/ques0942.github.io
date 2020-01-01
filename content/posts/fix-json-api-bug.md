---
title: "JSON APIのバグ修正"
date: 2020-01-02T00:09:00+09:00
draft: false
---

前回の記事の内容ではコンテンツにダブルクォーテーションなどのエスケープが必要な文字が入った場合にJSONとして不適切なレスポンスになるため修正する。

## 解決策
**item.json.json** を以下のように修正する

```
{{- dict "title" .Title "date" .Date "draft" .Draft "content" .Content | jsonify -}}
```

## 問題点
元々のitem.json.jsonでは、元データを変換せずにそのままレンダリングしてしまっていることが原因のため、何らかのエスケープを行えば解決する。

今回は以下のページを参考に、dictを作成してjsonifyで変換することにした。

* https://gohugo.io/functions/jsonify/#readout
* https://knooto.info/hugo-snippets/#json%E5%87%BA%E5%8A%9B
* https://blog.8-p.info/ja/2018/05/26/hugo-json/

