# Codex（OpenAI Codex CLI）連携ガイド

このドキュメントは `codex-cli 0.107.0` のヘルプ出力を基に更新しています（2026-03-04 確認）。

## Codex とは

Codex は OpenAI 製のターミナル型コーディングエージェントです。  
対話だけでなく、非対話実行（`exec` / `review`）で定型作業をまとめて処理できます。

## 先に修正した誤情報

以前の版にあった主な誤りは以下です。

- 設定ファイル拡張子は `~/.codex/config.yaml` ではなく `~/.codex/config.toml`
- 承認モード名は `Suggest / Auto Edit / Full Auto` ではなく、`untrusted / on-request / never`（`on-failure` は非推奨）
- `--approval-mode` ではなく `-a, --ask-for-approval` を使う
- `--full-auto` は存在するが、これは「低摩擦なサンドボックス実行」のエイリアス

## インストール

```bash
# npm
npm install -g @openai/codex

# または Homebrew cask
brew install --cask codex

# バージョン確認
codex -V
```

## 基本コマンド

```bash
# 対話モード
codex

# 非対話で単発実行
codex exec "テストを実行して失敗原因を修正して"

# 変更差分レビュー
codex review --uncommitted
```

## 重要オプション

```bash
# モデル指定
codex -m o3

# 承認ポリシー（対話実行でも利用可）
codex -a untrusted
codex -a on-request
codex -a never

# サンドボックス
codex -s read-only
codex -s workspace-write
codex -s danger-full-access

# 低摩擦実行（on-request + workspace-write）
codex --full-auto
```

## 設定ファイル（TOML）

`~/.codex/config.toml` に共通設定を書けます。

```toml
model = "o3"
```

承認ポリシーやサンドボックスは、バージョン差異を避けるためまず CLI オプション指定を推奨します。

```bash
codex -a on-request -s workspace-write
```

## Claude Code と連携して効率化する方法

### 方針

- Claude Code: 要件整理、設計判断、説明、最終レビュー
- Codex: 反復的な実装、テスト修正、機械的レビュー

### トークン消費を抑える実務パターン

1. Claude Code でタスクを小さく分割する  
   例: 「テスト修正」「lint修正」「型エラー修正」に分ける
2. 各タスクを Codex `exec` へ移譲する  
   例: `codex exec "pytest を実行して failing test をすべて修正"`
3. 結果だけ Claude Code に戻す  
   例: 変更ファイル一覧、要点、懸念点のみ共有
4. Claude Code は統合判断に集中する  
   例: 設計一貫性、仕様逸脱、リリース可否の確認

### 具体例

```bash
# 1) 実装をCodexに移譲
codex exec "docs配下を除いて、ruffとmypyのエラーを修正して"

# 2) 変更レビューをCodexに移譲
codex review --uncommitted

# 3) Claude Codeへは要約だけ渡す（長いログは渡さない）
# - 変更ファイル
# - 主要な修正方針
# - 未解決事項
```

## 運用ルール（推奨）

- 1タスク1目的で Codex に依頼する（プロンプトを短くする）
- ログ全文を Claude Code に貼らず、要点3行で返す
- 破壊的操作の可能性があるときは `-a untrusted` を使う
- 信頼できるリポジトリでも `danger-full-access` は原則使わない

## 参考リンク

- [OpenAI Codex CLI GitHub](https://github.com/openai/codex)
- [GitHub Agent HQ 公式ブログ](https://github.blog/jp/2026-02-05-pick-your-agent-use-claude-and-codex-on-agent-hq/)
