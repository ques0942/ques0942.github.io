---
title: "go-sqlmockのTips"
date: 2020-01-14T21:04:12+09:00
draft: true
---
## まとめ
* 動作確認したのは `v1.3.3`
* database/sqlのテストはgo-sqlmockを使うと比較的楽に行える
* クエリのmatcherには正規表現が使える
* Rowsからエラーを受け取りたい場合は、エラーが発生する行のデータを作る必要がある
* 不要なエラーのチェックだけならsqlmock.ExpectationsWereMetは呼び出さなくてもよい

## まえがき
golangでSQLを使うコードを書く場合、`database/sql` を使うことになるが、golangの標準ライブラリだけでこれのテストを書くことは非常に辛い。
おそらくDBを実際に用意してテストするしか無いが、様々なエラーケースのテストが必要な場合それだけではテストしきれない。
この記事では[DATADOG/go-sqlmock](https://github.com/DATA-DOG/go-sqlmock/)を使ってテストした際のTipsをまとめておく。

## はじめに
基礎的な部分は[README.md](https://github.com/DATA-DOG/go-sqlmock#tests-with-sqlmock)や[examples](https://github.com/DATA-DOG/go-sqlmock/tree/master/examples)、[godoc](https://godoc.org/github.com/DATA-DOG/go-sqlmock)を読めば事足りるが、以下に忘れがちな内容を記載する。


## Query matcher
クエリが実行されたかどうかをチェックするにあたって、呼び出された回数だけをチェックしたいケースが有る。
このような場合は、正規表現を使うことで対処できる。

```golang
mock.ExpectQuery(".*").WillReturnRows(rows)
```

雑だがgomockのAny()相当のことがしたい場合はこれで実現可能である。
また、QueryMatcher自体を自分でカスタマイズすることも可能だがこちらは試していない。

refs: https://godoc.org/github.com/DATA-DOG/go-sqlmock#QueryMatcher


## Rowsからエラーを受け取る
クエリの結果エラーを受け取りたい場合、 `ExpectedQuery.WillReturnRows` にエラーを含む `sqlmock.Rows` を登録することになるが、このとき以下のようにしてしまうとエラーが発生せずにハマることになる。

```golang
sqlmock.NewRows([]string{"ID"}).RowError(0, commonErr)
```

パッと見は最初の行でエラーが出そうであるが、これではエラーは出ない。
多分[この辺](https://github.com/DATA-DOG/go-sqlmock/blob/3f9954f6f6697845b082ca57995849ddf614f450/rows.go#L41)のせい。
以下のようにエラーが発生する行数になるまでAddRowを実行する必要がある。

```golang
sqlmock.NewRows([]string{"ID"}).AddRow(1).RowError(0, commonErr)
```


## 不要な呼び出しのチェック
[READMEのサンプル](https://github.com/DATA-DOG/go-sqlmock/tree/v1.3.3#tests-with-sqlmock)では`mock.ExpectationsWereMet` を実行してmockが呼び出されているか検証しているが、
SQLが実行されなかったことを保証するだけであれば
このメソッドを呼び出さずとも良い。
例えばvalidationの結果、SQLが実行されないようなケースのテストでそのようなテストが必要になる。  
ExpectXXX系のメソッドが呼ばれなかった場合に `db.QueryContext` のようなメソッドを実行すると、sqlmockがエラーを返すためそれを検証すれば良い。


