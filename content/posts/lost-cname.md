---
title: "Hugoで生成したコードをgithub pagesへデプロイするたびにカスタムドメインが初期化される"
date: 2020-01-03T12:18:34+09:00
draft: false
---

github actionsを使ってgithub pagesをデプロイしていると、デプロイのたびにカスタムドメインの設定が消えてしまっていたため原因を調べた。

## まとめ
* カスタムドメインを使用する場合はリポジトリルートの `CNAME` ファイルに指定したいドメインを記載する必要がある
* Hugoでは `static/CNAME` を作成すれば良い

## 起きていたこと
github pagesでカスタムドメインを使用する場合、 `settings` タブからカスタムドメインの設定を行うが、これは公開中のブランチ( `master` もしくは `gh-page`)の `CNAME` ファイルを作成、更新する形で設定される。
このため、Hugoなどで生成したコードをそれらのブランチに対してデプロイするような構成にしていた場合は、生成されたコードに `CNAME` ファイルが含まれていないとデプロイのたびにカスタムドメインの設定が無効になってしまう。  
refs: https://help.github.com/en/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site#configuring-a-subdomain

## Hugoでの解決策
`static/CNAME` に指定したいドメインを記載する。  
refs: https://gohugo.io/hosting-and-deployment/hosting-on-github/#use-a-custom-domain
