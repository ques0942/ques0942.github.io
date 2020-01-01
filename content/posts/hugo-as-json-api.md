---
title: "Hugo as JSON API"
date: 2020-01-01T23:11:43+09:00
draft: false
---

## やりたいこと
Hugoでブログを始めたものの、JSONでコンテンツをみることができると嬉しいので方法を調べてみた。  
ビルドに使用しているのはMac上のHugo(**Hugo Static Site Generator v0.62.0/extended darwin/amd64**)

## 参考URL
https://forestry.io/blog/build-a-json-api-with-hugo/

## やったこと
### config.tomlにoutput formatを追加
以下の内容を追加。HTMLのページ自体はそのまま残したいのでHTMLとJSONを指定する。

```toml
[outputs]
home = ["HTML", "JSON"]
section = ["HTML", "JSON"]
page = ["HTML", "JSON"]
```

### テンプレートを作成
**layouts/_default** 配下に以下のファイルを作成する。  
ファイル名は **{pageKind}.{outputFormatName}.{extension}** という形式になる。今回は **outputFormatName=json**、 **extension=json**となるので、JSON形式の出力の共通部分を記述した **baseof**、リスト表示用の **list**、単体表示用の **single**、それらの中で表示する1要素を表すための **item**の4つを作成する。

* baseof.json.json
* item.json.json
* single.json.json
* list.json.json



#### layouts/_default/baseof.json.json
```
{
    "data" : {{ block "response" .}}{{ end }}
}
```

<!-- TODO blockについて調べる -->
このブログのJSONレスポンスは全て**data** プロパティとして **response** ブロックをレンダリングして返す。
各 **pageKind**では **response**を定義する。

#### layouts/_default/item.json.json
```
{
  "title": "{{ .Title }}",
  "date": "{{ .Date }}",
  "draft": "{{ .Draft }}",
  "content": "{{ .Content }}"
}
```
ページのコンテンツから **title**、 **date**、 **draft**、 **content** を返すことにする。  
今後必要な項目が発生した場合はこれに追記することになる。

#### layouts/_default/single.json.json
```
{{ define "response" }} {{ .Render "item" }} {{ end }}
```
singleはコンテンツ1ページ分を返すためのpageKindなので、
itemをレンダリングして **response** を定義する。  
実際に表示すると以下のようになる。

```json
{
  "data": {
    "title": "First Post",
    "date": "2020-01-01 18:44:41 +0900 JST",
    "draft": "false",
    "content": "<p>ブログを放置しっぱなしだったので作り直してみる。<br>とりあえず手っ取り早くできる <strong>hugo + Github Page</strong> で作り直した。</p>"
  }
}
```
注: 見やすいように改行/空白の調整をしています。

#### layouts/_default/list.json.json
```
{{ define "response" }}
{
  {{ with .Section }}
  "section" : "{{ . }}",
  {{ end }}
  "count" : "{{ len .Data.Pages }}",
  "items" : [
  {{ range $i, $e := .Data.Pages }}
  {{ if $i }}, {{ end }}{{ .Render "item" }}
  {{ end }}
  ]
}
{{ end }}
```

listは複数のページを返すためのpageKindなので、**Section**が有ればそれと、含まれる **page**の数、及びそれぞれの **page** についての **item** をレンダリングする形で **response** を定義します。

## 結果の確認
コンテンツを **contents/posts** 配下に作っているものとする。

### list(Sectionあり)
[http://localhost:1313/index.json](http://localhost:1313/index.json)

### list(Sectionなし)
[http://localhost:1313/posts/index.json](http://localhost:1313/posts/index.json)

### single
[http://localhost:1313/posts/my-first-post/index.json](http://localhost:1313/posts/my-first-post/index.json)  
(コンテンツ名が **my-first-post** の場合)

