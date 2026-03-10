# リサーチ: 競合機能から導入可能な改善候補

調査日: 2026-03-10
調査タイプ: 複合（コードベース + 外部技術）

## 調査ゴール

research-2026-03-10-competitor-features.md で特定された「他にあって spec-driven-dev にない機能」7項目を、本プラグインで実現可能か・すべきかを評価する。

## 評価結果サマリ

| # | 機能 | 元ツール | 導入推奨度 | 難易度 | 理由 |
|---|------|---------|-----------|--------|------|
| 1 | Brownfield検証 | cc-sdd | ★★★ | 中 | analyzer 拡張で実現可能。既存コードとの統合は実案件で最も求められる |
| 2 | Project memory (Steering) | Kiro/cc-sdd | ★★☆ | 低 | CLAUDE.md + memory/ が既にあるが、プラグイン専用の steering は未整備 |
| 3 | EARS記法 | cc-sdd/Kiro | ★☆☆ | 低 | 導入は簡単だが、日本語ユーザーには馴染みが薄く効果が限定的 |
| 4 | Hooks自動化 | Kiro | ★★☆ | 中 | skill-tracker.sh は存在。フェーズ遷移の自動提案に拡張可能 |
| 5 | 複数AI対応 | cc-sdd/Spec Kit | ☆☆☆ | 高 | Claude Code 専用が差別化。対応するメリットなし |
| 6 | タスク進捗UI | Kiro | ★★☆ | 中 | Annotation Cycle の拡張で progress.md のビジュアル化は可能 |
| 7 | Constitution.md | Spec Kit | ★☆☆ | 低 | CLAUDE.md が同等の役割を果たしている |

## 詳細分析

### 1. Brownfield検証（★★★ 推奨）

**現状**: analyzer が既存コードパターンを発見するが、「既存コードとの統合リスク」を明示的に検証するフェーズがない

**cc-sdd の実装**: validate-gap（要件と既存コード差分）、validate-design（設計と既存構造の整合性）、validate-impl（実装と既存コードの整合性）の3段階

**導入案**:
- analyzer に「既存コード影響分析」セクションを追加
- spec スキルの Step 3（方向性確認）で既存コードとの衝突リスクを自動提示
- check スキルに「既存コードへの副作用」検証項目を追加

**工数**: analyzer.md と verifier の formats/output.md の拡張。スキル本体の変更は軽微

**効果**: 実案件（brownfield が大半）での信頼性が大幅に向上

### 2. Project memory / Steering（★★☆ 検討）

**現状**:
- CLAUDE.md: プロジェクト規約（手動管理）
- .claude/memory/MEMORY.md: Claude Code の auto-memory（スキル知見のみ）
- analyzer: 毎回 CLAUDE.md + Git 履歴を読み込む

**Kiro の実装**: `.kiro/steering/` に product.md / tech.md / structure.md を配置。全セッションで自動読み込み

**導入案**:
- `docs/steering/` に プロジェクト固有の技術方針・アーキテクチャ決定を蓄積
- analyzer が steering ファイルを自動読み込み
- spec/build 完了時に設計判断を steering に自動追記する hooks を追加

**懸念**: CLAUDE.md と steering の役割重複。Claude Code のエコシステムでは CLAUDE.md が標準なので、steering を別に持つ必要性が薄い

**代替案**: CLAUDE.md に「設計判断の蓄積」セクションを設け、spec 完了時に自動追記する仕組みだけ作る（新ファイル不要）

### 3. EARS記法（★☆☆ 見送り推奨）

**現状**: 自由形式の受入条件（AC-N チェックリスト）

**cc-sdd/Kiro の実装**: EARS（Easy Approach to Requirements Syntax）を要件記述に強制。5パターン（Ubiquitous, Event-driven, Unwanted, State-driven, Optional）で曖昧さ排除

**見送り理由**:
- 日本語での EARS 記法は不自然（英語前提の構文パターン）
- 現在の AC-N 形式で十分検証可能
- 導入コストに対して品質向上が限定的

### 4. Hooks自動化の拡張（★★☆ 検討）

**現状**: skill-tracker.sh がスキル名・セッション情報を追跡するのみ

**Kiro の実装**: ファイル変更をトリガーにエージェントを自動起動（例: コード変更→spec自動更新）

**導入案**:
- **フェーズ遷移の自動提案**: build 完了（progress.md の全タスク ✓）検出時に「/check を実行しますか？」を自動提示
- **plan.md 変更検知**: plan.md が手動編集された場合に progress.md との同期チェックを提案
- **result.md 生成時**: NEEDS_FIX 検出で「/spec で仕様を更新しますか？」を自動提示

**実装**: hooks.json に PostToolUse イベントを追加し、ファイル存在チェックスクリプトを実行

**注意**: Living document（コード変更→spec自動更新）は spec-flow の思想（仕様が実装をリード）と矛盾するため導入しない

### 5. 複数AI対応（☆☆☆ 見送り）

**理由**: Claude Code 専用であることが差別化ポイント。エージェント間の品質を最適化できるのは単一プラットフォームだからこそ。

### 6. タスク進捗UIの拡充（★★☆ 検討）

**現状**: Annotation Cycle は plan.md のレビューのみ

**Kiro の実装**: tasks.md と連動した進捗可視化UI

**導入案**:
- Annotation Viewer を拡張して progress.md のタスク一覧をビジュアル表示
- 各タスクの状態（未着手/実施中/完了）をカラーコード表示
- PR リンク・ブランチ情報を統合表示

**工数**: scripts/annotation-viewer/ の HTML/JS 拡張

### 7. Constitution.md（★☆☆ 見送り推奨）

**理由**: CLAUDE.md が同等の機能を提供。Claude Code エコシステムの標準に従うのが適切。

## 推奨・結論

**優先度高（次に取り組むべき）**:
1. **Brownfield検証の強化** — 実案件での最大の課題。analyzer + verifier の拡張で対応可能

**優先度中（余力があれば）**:
2. **Hooks によるフェーズ遷移の自動提案** — UX 改善。skill-tracker.sh の拡張で実現
3. **進捗UI の拡充** — Annotation Viewer の拡張で progress.md を可視化

**見送り**:
- EARS記法、複数AI対応、Constitution.md、Project memory（CLAUDE.md で代替）

## 次のステップ

- Brownfield検証を `/spec` で仕様化する場合は、analyzer.md と verifier の output フォーマットの具体的な変更設計が必要
- Hooks拡張は小規模なので独立して実装可能
