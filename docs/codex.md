# Codex（OpenAI Codex CLI）との連携

---

## Codex とは

Codex は OpenAI が開発したターミナルで動く AI コーディングエージェントです。
Claude Code と同様に自然言語でコード操作ができますが、特性が異なり **補完的な関係** にあります。

---

## Claude Code と Codex の違い

| 項目 | Claude Code | Codex |
|-----|------------|-------|
| 開発元 | Anthropic | OpenAI |
| モデル | Claude | GPT / o4-mini |
| 作業スタイル | 対話・協調ベース | 自律・成果ベース |
| 得意領域 | 高速実装・調査・説明 | CI/CD・コードレビュー・セキュリティ |
| 権限確認 | 細かく確認しながら進む | 自律実行（Full Auto モードあり） |
| ライセンス | - | OSS（Apache 2.0） |

### 使い分けの目安

- **Claude Code が得意**: 対話しながら実装・コードの説明・調査・GitHub 操作
- **Codex が得意**: 定型タスクの自動化・CI/CD・セキュリティチェック・高速プロトタイピング

---

## Codex のインストール

```bash
# グローバルインストール
npm install -g @openai/codex

# OpenAI API キーを設定
export OPENAI_API_KEY="your-api-key-here"

# 永続化する場合は .zshrc / .bashrc に追記
echo 'export OPENAI_API_KEY="your-api-key-here"' >> ~/.zshrc
```

---

## 基本的な使い方

### 対話モードで起動

```bash
codex
```

### 単発でタスクを実行

```bash
codex "このコードベースの構成を説明して"
codex "テストを全て実行して失敗しているものを修正して"
codex "セキュリティの脆弱性がないかチェックして"
```

### モデルを指定する

```bash
# デフォルトは o4-mini
codex -m o3 "リファクタリングして"
```

---

## 実行モード

Codex には 3 つの実行モードがあります：

| モード | 説明 | 用途 |
|-------|------|------|
| **Suggest**（デフォルト） | ファイル書き込み・コマンド実行を毎回確認 | 通常の作業 |
| **Auto Edit** | ファイル編集は自動、シェル実行のみ確認 | 実装作業の効率化 |
| **Full Auto** | すべて自動実行（確認なし） | 信頼できるタスクの完全自動化 |

```bash
# Full Auto モードで実行
codex --approval-mode full-auto "テストを修正して"
```

---

## GitHub Agent HQ での連携

GitHub Copilot Pro+ / Enterprise ユーザーは、GitHub 上で Claude Code と Codex を同じ Issue に対して並行してアサインし、結果を比較・組み合わせることができます。

### 使い方

1. GitHub Issue を開く
2. 右側のパネルから `Copilot` → エージェントを選択（Claude / Codex / Copilot）
3. 複数のエージェントを同じ Issue にアサインして比較することも可能

```
# Issue のコメントで直接メンション
@Claude この Issue を実装して
@Codex セキュリティの観点でレビューして
```

---

## Claude Code と Codex を組み合わせるワークフロー

### 実装フェーズごとに使い分ける

```
1. 設計・調査（Claude Code）
   「この機能をどう実装するか調べて、方針を教えて」

2. 実装（Claude Code）
   「その方針で実装して」

3. セキュリティレビュー（Codex）
   codex "実装したコードのセキュリティチェックをして"

4. テスト自動修正（Codex Full Auto）
   codex --approval-mode full-auto "失敗しているテストを修正して"

5. PR 作成（Claude Code）
   「変更内容をまとめて PR を作成して」
```

---

## カスタムスキルで Codex を呼び出す

`.claude/commands/` にスキルを定義して、Claude Code のセッション内から Codex を使うこともできます。

`.claude/commands/codex-security.md`：

```markdown
以下のコマンドを実行して、セキュリティチェックを行ってください：

```bash
codex "このコードベースのセキュリティ脆弱性を洗い出して、レポートを security-report.md に出力して"
```
```

呼び出し：

```
/codex-security
```

---

## 設定ファイル

Codex の設定は `~/.codex/config.yaml` で管理できます：

```yaml
model: o4-mini
approval_mode: suggest
notify: true
```

---

## 注意事項

- Codex の利用には **OpenAI API キー** が必要です（別途費用が発生します）
- `Full Auto` モードは信頼できるリポジトリ・タスクにのみ使用してください
- 機密情報を含むリポジトリで使用する際は、OpenAI のデータポリシーを確認してください
- API キーは `.env` ファイルや環境変数で管理し、コードに直接書かないでください

---

## 参考リンク

- [OpenAI Codex CLI GitHub](https://github.com/openai/codex)
- [GitHub Agent HQ 公式ブログ](https://github.blog/jp/2026-02-05-pick-your-agent-use-claude-and-codex-on-agent-hq/)
- [Codex vs Claude Code 比較](https://blog.openreplay.com/openai-codex-vs-claude-code-cli-ai-tool/)
