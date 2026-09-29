# 設計の根拠

このスキルと [[resolve-review-findings]] の決まりが、何を根拠にしているか。取得日はすべて 2026-09-27。**確認**は原文と照合したもの、**報告**はサブエージェントの調査報告に基づき原文照合をしていないもの。

## 前の版で起きたこと

旧 multi-angle-review（Claude 4 系の時期に実験的に作成）を backlog-mcp-prototype で使ったとき（2026-09-06〜09-08）に起きた問題。

- 基準の宣言が無く、申し送り用の設計メモや README が違反の根拠として持ち込まれた → 手順1の宣言
- 台帳に巡ごとの追記が重なり、撤回済みの指摘が残るなど読めなくなった → 上書き・現在の状態だけ
- 推奨と状態で語彙が2系統あり、修正した AI が自分で「修正済」を付けた → 状態を1系統に、付ける人を固定
- 指摘の文面だけで原因を推測して束ね、10のうち4が誤っていた → 同根は候補にとどめ、確定は修正側がソースで行う
- 修正作業を巡に数えた／直しきる前に再レビューへ進もうとした → 巡の定義と再レビューの入る条件
- 統合役がレビュアーに渡した表に誤りがあり、レビュアーがそれを前提にした → 統合役の要約を渡さない
- 状態が台帳・作業用ディレクトリ・引き継ぎ文書・日次ログに分散した → 置き場所の表

## Anthropic の公表（確認）

- Claude Opus 5.5 System Card（2026-09-22）：社内利用で最も多い要注意の振る舞いは「確かめていない推論を確立した事実として述べる」。「自分で書いた要件に照らして計画を確かめる」例、サブエージェントへユーザーが書いていない承認の文言を渡した例（0.01% 未満）
  - https://www-cdn.anthropic.com/fc1b44717c85dc068bc6ba5024219938094694bd/Claude%20Opus%205.5%20System%20Card.pdf
- Prompting Claude Fable 5：「Separate, fresh-context verifier subagents tend to outperform self-critique.」「Report your findings and stop.」
  - https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5.md
- Prompting Claude Fable 5.1：「scaling it down is the user's call, not yours」
  - https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5-1.md
- Prompting Claude Sonnet 5：レビューで「保守的に」などと書くと文字どおりに報告を絞る。全件を確信度付きで出させ、絞り込みは別の段で行う
  - https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-sonnet-5.md
- Prompting Claude Opus 5.5：止まらずに進ませる追記は、人間が関わる用途には入れない
  - https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5-5.md
- Claude Code subagents：サブエージェントのログは `~/.claude/projects/{project}/{sessionId}/subagents/agent-{agentId}.jsonl`。同じセッションを再開すれば SendMessage で呼び戻せる。`cleanupPeriodDays`（既定30日）で削除。Explore と Plan は再開できない（Claude Code v2.1.280 で確認）
  - https://code.claude.com/docs/en/sub-agents.md

## Anthropic の公表（報告）

- Harness design for long-running application development（2026-03-24）：作る側と判定する側を分けるのが強い手段。素の Claude は問題を見つけても大したことはないと自分を説得して通してしまう
  - https://www.anthropic.com/engineering/harness-design-long-running-apps
- How we built our multi-agent research system（2025-06-13）：サブエージェントの出力はファイルに残し、伝言ゲームを避ける
  - https://www.anthropic.com/engineering/multi-agent-research-system
- 公式 code-review プラグイン：指摘ごとに別の検証役を立てる。規則違反は規則を原文で引用できる場合に限る
  - https://github.com/anthropics/claude-code/blob/main/plugins/code-review/commands/code-review.md

## 研究（報告。題名のみ確認したものに ※）

- 自分のものというラベルだけで評価が上がる。盲検で消える（arXiv 2608.18091 ※、EMNLP Findings 2026）
- 誤りを自分のものとして示すと直せず、外から来たものとして示すと直せる（arXiv 2507.02778 ※、COLM 2026）
- 前回の点数を文脈に入れると判定が引っ張られ、警告しても消えない（arXiv 2608.25869 ※、CIKM '26）
- 1ラウンドの討論で満場一致が増えるが正答率は変わらない。検証は相互に見せる前に行う（arXiv 2609.26145 ※、査読未確認）
- 統合者に文章を合成させるより、候補から選ばせるほうが強い（arXiv 2603.20324 ※）
- 能力が上がるほどモデル同士の誤りは似る（arXiv 2502.04313、2506.07962）
