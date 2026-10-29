# suikou

日本語の文章を、読み手が一度読んで分かる形に推敲する Claude Code のスキルです。
名前の suikou は「推敲」の読みです。

## 何をするか

- 抜けた主語・目的語・指す先を補う。読み手が補えない省略だけを直す。
- 読み手の知識に合わせて、語と説明の深さを選ぶ。
- 理由や条件を落とさずに、短くする。

次のものは担当しません。誤字脱字・表記ゆれだけの校正、事実の確認、翻訳、コードの短縮、キャラクター口調への変換、内容そのものの企画です。

判定の基準は 1 つです。読み手が、その文と直前の文だけを読んで、誰が・何を・どうするかを答えられるか。省略が文法として自然かどうかでは決めません。

## 入れ方

Claude Code で、次の 2 つを実行します。

```text
/plugin marketplace add sai-kaneko-31/suikou
/plugin install suikou@suikou
```

スキルだけを使うときは、`plugins/suikou/skills/suikou` を `~/.claude/skills/suikou` にコピーします。この場合、更新は手動になります。

## 使い方

文章を渡して、「読みづらいと言われた」「直して」「もう少し短くして」のように頼みます。スキル名を言わなくても、説明文に当てはまる依頼なら使われます。名前を指定するときは `/suikou:suikou` です。

## テスト

`plugins/suikou/evals/` に、スキルが期待どおりに動くかを確かめるテストケースがあります。1 ケースが 1 フォルダで、依頼の文（`prompt.md`）、期待する結果（`case.yaml`）、採点の項目（`graders/`）が入っています。

Claude Code v2.1.269 以降なら、プラグインのフォルダで次のコマンドを実行すると、スキルあり・なしの両方で全ケースを回します。実際にモデルを動かすので、料金がかかります。

```text
cd plugins/suikou
claude plugin eval . --allow-tools Write,Edit,Bash
```

## 着想

「先に口頭で説明できる文章にしてから、短くする」という考え方は、Zenn の記事「[AI の謎な説明文を直すスキル](https://zenn.dev/microsoft/articles/explain-before-shorten)」から得ました。手順と文面は、この README の作者が独自に書いています。

## ライセンス

MIT ライセンスです。全文は [LICENSE](LICENSE) にあります。
