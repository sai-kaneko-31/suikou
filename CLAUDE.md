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
  - zip は、マージ済みの main から作る: `git archive --format=zip --prefix=suikou/ -o suikou-skill-<コミット>.zip HEAD:plugins/suikou/skills/suikou`
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
- 結果は `plugins/suikou/evals/results/` に出る（`claude plugin eval --help` による）
