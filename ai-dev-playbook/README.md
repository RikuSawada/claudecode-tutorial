# AI開発ワークフロー Playbook

Claude Code（マネージャー・アーキテクト）とCodex（開発者）をGitHubと連携し、
実装速度と品質を両立する開発ワークフローの社内標準ドキュメント。

---

## 対象読者

- このフローをチームに導入したいエンジニア・テックリード
- AIを使って開発速度を上げたいが、品質やセキュリティが不安な人
- レビュー工数を減らしつつ品質を落としたくないチーム

---

## ファイル構成

| ファイル | 内容 |
|---------|------|
| [quickstart.md](quickstart.md) | 30分で体験するクイックスタート・チーム展開手順 |
| [development-flow.md](development-flow.md) | 全体フロー・役割分担・工程別AI活用・CI/CD・品質KPI・段階導入 |
| [ai-usage-guidelines.md](ai-usage-guidelines.md) | AIの使い方・コンテキスト設計・人間レビュー必須ポイント・禁止領域 |
| [prompt-template.md](prompt-template.md) | 工程別プロンプトテンプレート集（コピペで使える） |
| [issue-template.md](issue-template.md) | Issueの書き方・テンプレート・スコープ管理 |
| [pr-template.md](pr-template.md) | PRテンプレート・レビューチェックリスト・マージ条件 |
| [repo-template.md](repo-template.md) | ディレクトリ構成・命名規約・CI設定・CODEOWNERS |

---

## はじめての人へ

1. **[quickstart.md](quickstart.md)** — 30分で手を動かしてフローを体験する
2. **[development-flow.md](development-flow.md)** — 全体像と役割分担を把握する
3. **[ai-usage-guidelines.md](ai-usage-guidelines.md)** — AIに任せる範囲と禁止領域を確認する
4. **[prompt-template.md](prompt-template.md)** — 手元に置いて実作業を始める
5. **[repo-template.md](repo-template.md)** — リポジトリに設定ファイルを追加する

---

## ツール前提

| ツール | 役割 | インストール |
|--------|------|------------|
| Claude Code | マネージャー・アーキテクト（計画・設計・レビュー支援） | `npm install -g @anthropic-ai/claude-code` |
| Codex CLI | 開発者（実装・テスト実装・リファクタ） | `npm install -g @openai/codex` |
| GitHub | Issue/PR/CI/CD の母艦 | — |

---

## 基本原則

- **決定と責任は人間が持つ**：AIは実行者。承認・判断・レビューは必ず人間が行う
- **小さく作って早くマージ**：PRは1機能1目的。大きなPRはレビューもAIも精度が落ちる
- **コンテキストを渡し続ける**：AIの精度はコンテキストの質で決まる。CLAUDE.mdを育てる
- **禁止領域は明示的に守る**：セキュリティ・認証・決済は人間がレビューを必須とする
