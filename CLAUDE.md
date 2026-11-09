# suikou

日本語の文章を推敲する Claude Code のスキル。1 つのリポジトリがマーケットプレイスとプラグインを兼ねる。

## 構成

- `.claude-plugin/marketplace.json`: マーケットプレイスの定義。version は書かない
- `plugins/suikou/.claude-plugin/plugin.json`: プラグインの定義。version はここにだけ書く
- `plugins/suikou/skills/suikou/SKILL.md`: スキル本体。手順 1〜6 と、型 10 種
- `plugins/suikou/skills/suikou/references/patterns.md`: 型ごとの見分け方と例。SKILL.md の手順 2 から参照される
- `plugins/suikou/evals/<ケース名>/`: 評価ケース

## SKILL.md と patterns.md を変えるとき

- 型の見分け方は SKILL.md と patterns.md に同じ項目がある。「次の N 点」の数と項目は両方で揃える（構造は 9 点）
- 観点を足したら、SKILL.md の「出す前の確認」と、patterns.md の例も足す。直してはいけない場合は、patterns.md の「直さずに残す例」に足す
- patterns.md の例は「前」と「後」の 2 行で書く。「文脈」の行は、必要な例にだけ付ける。「直さずに残す例」の節だけは「原文」と「判断」で書く
- 担当範囲の説明は、SKILL.md（description と「前提と担当範囲」）、README の「何をするか」、plugin.json と marketplace.json の description にある。範囲を変えたら全部揃える

## リリース

- スキルの中身を変えて公開するときは、plugin.json の version を上げる。上げないと、マーケットプレイスから入れた利用者は更新を受け取らない。根拠は https://code.claude.com/docs/en/plugins/host-marketplace
- Agensi の掲載ページは、このリポジトリとは別に更新する。最新の版はこのリポジトリ
  - zip は origin/main から作る: `git archive --format=zip --prefix=suikou/ -o suikou-skill-<コミット>.zip origin/main:plugins/suikou/skills/suikou`
  - 作る前に `git fetch origin` を打つ。`<コミット>` は、`git rev-parse --short origin/main` の値
  - 画面の道順: Dashboard の Creator Studio へ移る。suikou の行にある鉛筆（編集）を押し、「Replace Skill ZIP」へ zip を上げる。Changelog を書いたら、Resubmit を押す。確認画面が出たら、Submit for review を押す。鉛筆の隣は削除のゴミ箱
  - 審査は通常 24〜48 時間。通るまで現行版は公開のまま。Agensi の版（1.1 など）は、plugin.json の version とは別に採番される
  - zip を上げると、項目が frontmatter から自動入力される。送る前に、名前・要約・説明・権限が掲載のままかを見る
- マニフェストの検証は、リポジトリ直下で `claude plugin validate .`
- PR は merge commit でマージする。head ブランチは、マージのときに自動的に消える

## 評価ケース

- 1 ケース 1 フォルダ。中身は次の 3 つ
  - `case.yaml`: schema_version "1.1"・name・expected_outcome
  - `prompt.md`: frontmatter は tags・max_turns・timeout_seconds・allowed_tools
  - `graders/NN-名前.md`: frontmatter は type・focus・weight
- 実行: `cd plugins/suikou && claude plugin eval . --allow-tools Write,Edit,Bash`。モデルを動かすので料金がかかる。スキルなしの基準も同時に回る
- 既定は 1 ケース 3 回。1 ケースだけ回すときは `--case <ケース名>`、回数は `--runs <n>` で絞る（`claude plugin eval --help` による）
- アカウントが対応しているときは、既定でレポート（スコア・プロンプト・採点の判定）を claude.ai へ公開する。`--no-publish` を付けると手元だけに残る（`claude plugin eval --help` による）。実行する前に、持ち主へ公開してよいかを聞く
- 結果は `plugins/suikou/evals/results/` に出る（`claude plugin eval --help` による）。git には入らない（`.gitignore` の `**/evals/results/`）。worktree を消すと、結果も一緒に消える
- ケースを足す・変えるときは、PR を出す前に、そのケースだけ `--case <ケース名> --runs 2 --no-publish` で回す。2 ケースを 1 回ずつ回した観測では、1 ケースが 2 分前後、$0.3 前後だった
- 規則の効果を確かめるケースは、書く前にスキルなしの出力を見る。スキルなしでも通る入力は、規則の効果を区別できない。結果の差（Δ）が 0 以下のケースは、入力を作り直す。全アームで通っても、差が 0 なら区別できていない
- SKILL.md や patterns.md を変えたときは、関係しそうなケースを回し、スコア・差・回数を PR 説明文に書く
- 採点の判定の理由は、結果に残らない（票だけが残る）。grader が落ちた原因は、`--keep-temp` を付けて回し直し、出力と grader の文を 1 文ずつ突き合わせて確かめる（`--keep-temp` は、実行の出力にある案内による）
- 判定役（judge）が見る trace に、依頼文（`prompt.md`）は入らない。`aggregate-result.json` の `graders[].evidence` が judge の見た trace で、`focus: trace` の 3 回で、user の行はツールの結果とスキルの本文だけだった。依頼文の背景と照らす grader は、背景を grader の本文に書く
- 基準を決める前に、手で書いた模範解答を 1 つ judge に通し、通ることを確かめる。スキルあり・なしの両方が毎回落ちる grader は、基準が厳しすぎるか、judge が必要な情報を見られていない
