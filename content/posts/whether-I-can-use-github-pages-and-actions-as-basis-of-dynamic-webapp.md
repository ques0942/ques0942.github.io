---
title: "動的なサイトをGithubPagesとActionsで作れるか?"
date: 2020-01-08T18:52:48+09:00
draft: true
---

結論から言うとGithub Actionsだけでは無理そう。
[API](https://developer.github.com/v3/repos/#create-a-repository-dispatch-event)叩けばActionsを起動できるかと思ったが、
`repo` スコープのpersonal tokenが必要になるのでjsから直接叩くのは現実的ではない。
issueを作成するとかが現実的か? TODO: issueの作成権限についても調査が必要。


