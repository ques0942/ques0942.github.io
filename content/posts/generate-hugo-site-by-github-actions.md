---
title: "GitHub Actionsを使ってHugo製のサイトをデプロイする"
date: 2020-01-02T01:17:02+09:00
draft: true
---

この[action](https://github.com/peaceiris/actions-gh-pages)を使えば難しいことはない。
[getting-started](https://github.com/peaceiris/actions-gh-pages#getting-started)に書いてあることをそのまま実施すればOK。
今のこのサイトでは `master` ブランチを公開する設定にしているため、
`source` ブランチに生成元のコードを作成し、それらをもとにHugoで生成したファイルを `master` ブランチに展開している。
そのための設定は以下のようになる。

### トリガーとなるブランチの変更
```diff
 on:
   push:
     branches:
-    - master
+    - source
 
 jobs:
   build-deploy:
```

### デプロイ先のブランチの変更
```diff
       uses: peaceiris/actions-gh-pages@v2
       env:
         ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
-        PUBLISH_BRANCH: gh-pages
+        PUBLISH_BRANCH: master
         PUBLISH_DIR: ./public
```

通常のgitの運用フローとかなり異なるため、
気に入らないようであれば[外部レポジトリへのデプロイ機能](https://github.com/peaceiris/actions-gh-pages#%EF%B8%8F-deploy-to-external-repository)を使ってページの生成元コード群と、公開ページのレポジトリを分けてしまっても良いだろう。
