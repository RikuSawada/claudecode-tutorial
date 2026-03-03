以下の手順でプロジェクトの初期設定ファイルを作成してください。

## 手順

### 1. プロジェクト情報の確認

まず以下を調べてください：
- プロジェクトルートにある `package.json`、`README.md`、主要なソースファイルを読む
- 使われている言語・フレームワーク・テストツール・パッケージマネージャーを特定する
- ディレクトリ構成を把握する

### 2. CLAUDE.md を作成

プロジェクトルートに `CLAUDE.md` を作成してください。
調査した内容を元に、以下のテンプレートを**実際の情報で埋めて**作成すること。
空欄やプレースホルダーのままにしないこと。

```markdown
# CLAUDE.md

## Claude Code への注意事項

- 挨拶・前置き・段階報告・絵文字禁止。結論ファースト
- 指摘すべきことは率直に指摘
- 実装前に必ず該当ファイルを読んでから変更すること
- テストなしで実装を完了させないこと
- 破壊的な変更（DB スキーマ変更など）は事前に確認を取ること
- コミットは日本語でわかりやすく書くこと

## 役割

- タスク管理のマネージャ
- システム開発

## コンテンツワークフロー

- ブレスト・構成の中間成果は `ideas/[YYYYMMDD]-[topic].md` に即保存
- 長時間タスクはステップ分割し、各完了後にファイル保存
- 説明には必ず具体例を含める

## Plan Mode

- プランファイルには**意図**（なぜ必要か）と**選択理由**を含める

## プロジェクト概要

（プロジェクトの目的・技術スタック・特記事項を記載）

## 開発環境セットアップ

​```bash
（調査したコマンドを記載。例: npm run dev / npm test など）
​```

## アーキテクチャ

（主要なディレクトリとその役割・設計方針を記載）

## コーディング規約

（言語・フレームワークから推測できる規約を記載）

## 注意事項

（プロジェクト固有で Claude Code に守ってほしいルールを記載）

## 禁止事項

（Claude Code に実行してほしくない操作を記載）
```

### 3. .claude/settings.json を作成

`.claude/settings.json` を作成してください。
調査した内容を元に、プロジェクトで実際に使うコマンドを allow に追加すること。

```json
{
  "permissions": {
    "allow": [
      "Read(*)",
      "Write(*)",

      （調査したパッケージマネージャーに合わせたコマンドを追加）
      "Bash(npm install*)",
      "Bash(npm run dev)",
      "Bash(npm run build)",
      "Bash(npm run start)",
      "Bash(npm test)",
      "Bash(npm run test*)",
      "Bash(npm run lint)",
      "Bash(npm run format)",
      "Bash(npx *)",

      "Bash(git status)",
      "Bash(git diff*)",
      "Bash(git log*)",
      "Bash(git add *)",
      "Bash(git commit*)",
      "Bash(git checkout *)",
      "Bash(git switch *)",
      "Bash(git branch*)",
      "Bash(git fetch*)",
      "Bash(git pull*)",
      "Bash(git stash*)",
      "Bash(git restore *)",

      "Bash(ls*)",
      "Bash(pwd)",
      "Bash(echo *)",
      "Bash(cat *)",
      "Bash(mkdir *)",
      "Bash(cp *)",
      "Bash(mv *)",

      "Bash(node *)",
      "Bash(which *)",
      "Bash(env)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force*)",
      "Bash(git reset --hard*)",
      "Bash(git clean -f*)",
      "Bash(sudo *)",
      "Bash(chmod 777 *)"
    ]
  }
}
```

### 4. 完了報告

作成したファイルの内容を簡潔に報告してください。
