# リサーチ: 仕様駆動開発ツール比較（cc-sdd / Kiro / GitHub Spec Kit）

調査日: 2026-03-10
調査タイプ: 外部技術

## 調査ゴール

spec-driven-dev の差別化ポイントを機能レベルで明確にするため、競合3ツールの具体的な機能を把握する。

## 調査結果

### 比較表

| 観点 | cc-sdd | Kiro | GitHub Spec Kit | spec-driven-dev |
|---|---|---|---|---|
| 提供形態 | npm CLI | SaaS IDE | Python/uv CLI | Claude Code スキル |
| 対応 AI ツール | 8種類 | Kiro 専用 | 20種類以上 | Claude Code 専用 |
| フェーズ構成 | 要件→設計→タスク→実装 | 要件→設計→タスク | 憲法→要件→計画→タスク→実装→PR | spec→build→check→fix |
| コマンド数 | 11 | GUI 操作 | 8 | 5スキル |
| 要件記法 | EARS 記法 | EARS 記法 | 自由形式 | 自由形式 |
| 生成ファイル | requirements/design/tasks.md | requirements/design/tasks.md | constitution/spec/plan.md + tasks/ | plan.md + progress.md |
| project memory | `.kiro/steering/` | `.kiro/steering/`(inclusion mode) | constitution.md | なし |
| 既存コード対応 | brownfield(validate-gap等) | 汎用 | なし | なし |
| 仕様-実装突合 | validate-impl | 自動spec更新(living doc) | `/speckit.analyze` | check(双方向突合+8項目検証) |
| 修正ポリシー | なし | なし | なし | 推測修正禁止 |
| レビューUI | なし | タスク進捗UI | なし | Annotation Cycle |
| 技術調査機能 | なし | なし | なし | research スキル |
| 価格 | OSS無料 | 無料〜$39/月 | OSS無料 | OSS無料 |

### spec-driven-dev が持っていて他にない機能

1. **fix の推測修正禁止ポリシー** — 原因特定を義務付け、推測変更を明示禁止。他3ツールは許容
2. **research スキル** — 専用の技術調査フェーズ。4エージェント構成。他3ツールは技術調査フェーズなし
3. **writer の8項目自己検証** — データフロー↔API、API↔DB、API↔フロント等の整合性を生成時に検証
4. **Annotation Cycle** — ブラウザで plan.md をインラインレビュー。差分ハイライト+自動修正ループ
5. **ファイル存在によるフェーズ推論** — state.json 不使用。plan.md/progress.md/result.md の存在で判定

### 他にあって spec-driven-dev にない機能

1. **EARS 記法** — cc-sdd/Kiro が使用。要件の曖昧さ排除
2. **brownfield 検証** — cc-sdd の validate-gap/design/impl。既存コードへの安全な統合
3. **project memory (Steering)** — セッション横断のプロジェクト知識永続化
4. **constitution.md** — Spec Kit のプロジェクト原則ファイル
5. **Hooks（イベント駆動自動化）** — Kiro のファイル操作連動エージェント起動
6. **複数 AI ツール対応** — cc-sdd(8種)、Spec Kit(20種以上)
7. **タスクリアルタイム進捗 UI** — Kiro の tasks.md 連動 UI

### 各ツールの特徴的な設計判断

**cc-sdd**: 8プラットフォーム統一のマルチAI戦略。EARS記法強制。9サブエージェント並列実行。brownfield ワークフロー充実
**Kiro**: IDE統合で UX 最優先。Living document（コード変更→spec自動更新）。Spec/Vibeの二本立て。月額課金
**Spec Kit**: constitution.md によるチーム原則の明文化。plan で2,067行生成するケースあり（ドキュメント過多の批判）

## 推奨・結論

記事の差別化軸として有効なのは:
1. **「推測修正禁止」と「技術調査フェーズ」は唯一の機能** — 他ツールにない独自性
2. **check の双方向突合** — validate-impl(cc-sdd)や analyze(Spec Kit)は片方向。living document(Kiro)は自動だが精度不明
3. **Annotation Cycle** — 他ツールにないブラウザレビュー体験
4. **逆に弱い点も正直に語れる** — EARS記法なし、マルチAI非対応、Steering なし

## 出典

- [GitHub - gotalab/cc-sdd](https://github.com/gotalab/cc-sdd)
- [Kiro - Specs Documentation](https://kiro.dev/docs/specs/)
- [Kiro - Steering Documentation](https://kiro.dev/docs/steering/)
- [GitHub - github/spec-kit](https://github.com/github/spec-kit)
- [GitHub Blog - Spec Kit](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
- [Scott Logic Blog - Spec Kit Review](https://blog.scottlogic.com/2025/11/26/putting-spec-kit-through-its-paces-radical-idea-or-reinvented-waterfall.html)
- [Martin Fowler - SDD 3 Tools](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
