---
tags: [suikou]
max_turns: 20
timeout_seconds: 600
allowed_tools: [Read, Glob, Grep, Skill, Write, Edit, Bash]
---

社内の運用担当者向けの手順書の 1 文を、もっと簡単にしてほしい。「難しい」と言われた。

背景: 手順書のほかの節と、API の仕様書には、「冪等」という語が出てくる。読み手は運用担当者で、プログラミングはしない。

今の文:
この API は冪等なので、ネットワークエラーで失敗した場合は、同じリクエストを再送してかまいません。
