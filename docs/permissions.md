# 権限・安全設定

---

## 概要

Claude Code はファイル編集・コマンド実行などの操作を行う際、
ユーザーの承認を求める仕組みを持っています。

この「権限システム」を理解することで、
**安全に使いながら作業効率も最大化** できます。

---

## 権限モードの種類

Claude Code には 4 つの権限モードがあります：

| モード | 説明 | 推奨シーン |
|-------|------|----------|
| **デフォルト** | 危険な操作のみ確認を求める | 通常の開発作業 |
| **自動承認** | すべての操作を自動で実行 | 信頼できる作業・スクリプト自動化 |
| **読み取り専用** | ファイル読み取りのみ許可 | コードレビュー・調査 |
| **カスタム** | 操作ごとに細かく設定 | 高度なカスタマイズ |

---

## 操作ごとの承認レベル

### 自動で許可される操作（低リスク）

- ファイルの読み取り
- ディレクトリの一覧表示
- Git の状態確認（`git status`, `git log`）
- テストの実行（読み取り専用なコマンド）

### 確認が必要な操作（中リスク）

- ファイルの作成・編集
- `git add` / `git commit`
- パッケージのインストール（`npm install`）

### 必ず確認される操作（高リスク）

- ファイルの削除
- `git push`・ブランチの削除
- `git reset --hard`
- 環境変数の変更
- PR・Issue のマージ・クローズ
- 外部サービスへのデータ送信

---

## 自動承認の設定

### CLI オプションで指定する

```bash
# すべての操作を自動承認（注意して使用）
claude --dangerously-skip-permissions

# 読み取り専用モードで起動
claude --read-only
```

### 設定ファイルで細かく制御する

`~/.claude/settings.json` または `.claude/settings.json`：

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(npm run lint)",
      "Bash(git status)",
      "Bash(git diff*)",
      "Read(*)",
      "Write(src/**)"
    ],
    "deny": [
      "Bash(rm *)",
      "Bash(git push --force*)",
      "Bash(git reset --hard*)"
    ]
  }
}
```

### 許可ルールの書き方

| パターン | 意味 |
|---------|------|
| `Read(*)` | すべてのファイルの読み取りを許可 |
| `Write(src/**)` | src 配下のすべてのファイルへの書き込みを許可 |
| `Bash(npm test)` | `npm test` コマンドを自動承認 |
| `Bash(git diff*)` | `git diff` で始まるコマンドを自動承認 |
| `Bash(rm *)` | `rm` コマンドを拒否（deny リスト） |

---

## プロジェクト別の設定

プロジェクトごとに権限を設定することで、リポジトリに応じたルールを適用できます。

`.claude/settings.json`（プロジェクトルート）：

```json
{
  "permissions": {
    "allow": [
      "Bash(npm *)",
      "Bash(git add *)",
      "Bash(git commit *)",
      "Bash(git status)",
      "Bash(git log*)",
      "Bash(git diff*)",
      "Read(*)",
      "Write(src/**)",
      "Write(tests/**)"
    ],
    "deny": [
      "Bash(git push --force*)",
      "Bash(rm -rf *)",
      "Bash(DROP TABLE*)"
    ]
  }
}
```

---

## 安全に使うための指針

### やってはいけないこと

```bash
# 本番環境では使わない
claude --dangerously-skip-permissions

# 本番DBへの接続情報を渡さない
# MCP で本番DBを直接接続しない
```

### 推奨する設定

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(npm run build)",
      "Bash(npm run lint)",
      "Bash(git status)",
      "Bash(git log*)",
      "Bash(git diff*)",
      "Bash(git add *)",
      "Bash(git commit *)"
    ],
    "deny": [
      "Bash(git push --force*)",
      "Bash(git reset --hard*)",
      "Bash(rm -rf *)",
      "Bash(sudo *)"
    ]
  }
}
```

### 操作前に内容を確認する習慣

Claude Code が操作を提案した際、承認前に必ず確認してください：

```
承認前のチェックリスト：
□ どのファイルが変更されるか把握しているか
□ 実行されるコマンドの意味を理解しているか
□ 元に戻せない操作ではないか
□ 影響範囲は想定内か
```

---

## 機密情報の取り扱い

### Claude Code に渡してはいけない情報

- 本番環境のパスワード・APIキー
- 個人情報・顧客データ
- 社外秘の情報

### 安全な代替手段

```bash
# 環境変数を使う（コードに直接書かない）
export DATABASE_URL="..."
export API_KEY="..."

# .env ファイルを使い、.gitignore に追加する
echo ".env" >> .gitignore
```

---

## 緊急時の対処

### Claude が意図しない操作をしようとした場合

1. **拒否ボタン** を押してすぐに止める
2. 何が起きようとしていたかを確認する
3. 「〇〇の操作はしないで」と伝えて再依頼する

### 誤ってファイルを変更された場合

```bash
# Git で変更を確認
git diff

# 変更を元に戻す
git checkout -- path/to/file

# 複数ファイルをまとめて戻す
git restore .
```

### 誤ってコミットされた場合

```bash
# 直前のコミットを取り消す（変更は残す）
git reset HEAD~1

# 特定のコミットを打ち消す（履歴は残す）
git revert <commit-hash>
```

---

## 権限に関するログ確認

Claude Code が実行した操作はセッション内で確認できます：

```
「このセッションで実行したコマンドを一覧表示して」
「どのファイルを変更したか教えて」
```
