# Hooks（フック設定）

---

## Hooks とは

Hooks は **Claude Code のツール実行前後に自動でスクリプトを走らせる仕組み** です。

例えば：
- ファイルを保存するたびに自動でフォーマット
- コマンド実行前にログを記録
- 特定のコマンドをブロックして安全を担保

---

## フックの種類

| フック名 | タイミング | 用途 |
|---------|----------|------|
| `PreToolUse` | ツール実行**前** | バリデーション・ブロック・ログ |
| `PostToolUse` | ツール実行**後** | フォーマット・通知・後処理 |
| `Notification` | 通知発生時 | デスクトップ通知・Slack 連携 |
| `Stop` | Claude が応答完了時 | 完了通知・後処理 |

---

## 設定ファイルの場所

```
~/.claude/settings.json           # 全プロジェクト共通
your-project/.claude/settings.json  # プロジェクト限定
```

---

## 基本的な設定形式

```json
{
  "hooks": {
    "フック名": [
      {
        "matcher": "対象のツール名（省略で全ツール）",
        "hooks": [
          {
            "type": "command",
            "command": "実行するシェルコマンド"
          }
        ]
      }
    ]
  }
}
```

---

## 実践的なフック設定例

### 1. ファイル保存後に自動フォーマット

ファイルを編集するたびに Prettier を自動実行します：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "npx prettier --write \"$CLAUDE_TOOL_OUTPUT_FILE_PATH\" 2>/dev/null || true"
          }
        ]
      }
    ]
  }
}
```

---

### 2. ファイル保存後に ESLint を自動実行

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "npx eslint --fix \"$CLAUDE_TOOL_OUTPUT_FILE_PATH\" 2>/dev/null || true"
          }
        ]
      }
    ]
  }
}
```

---

### 3. 危険なコマンドをブロックする

`rm -rf` など危険なコマンドをブロックしてアラートを出します：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"$CLAUDE_TOOL_INPUT\" | grep -qE 'rm -rf|DROP TABLE|git push --force' && echo 'BLOCKED: 危険なコマンドが検出されました' && exit 1 || exit 0"
          }
        ]
      }
    ]
  }
}
```

---

### 4. コマンド実行ログを記録する

すべての Bash コマンドをファイルに記録します：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"[$(date '+%Y-%m-%d %H:%M:%S')] $CLAUDE_TOOL_INPUT\" >> ~/.claude/command-history.log"
          }
        ]
      }
    ]
  }
}
```

---

### 5. 作業完了時に通知する（macOS）

Claude が回答を完了したときにデスクトップ通知を出します：

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"Claude Code の作業が完了しました\" with title \"Claude Code\"'"
          }
        ]
      }
    ]
  }
}
```

---

### 6. 作業完了時に Slack 通知する

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "curl -s -X POST -H 'Content-type: application/json' --data '{\"text\":\"Claude Code の作業が完了しました\"}' $SLACK_WEBHOOK_URL"
          }
        ]
      }
    ]
  }
}
```

---

### 7. TypeScript ファイル保存後に型チェック

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "FILE=\"$CLAUDE_TOOL_OUTPUT_FILE_PATH\"; [[ \"$FILE\" == *.ts || \"$FILE\" == *.tsx ]] && npx tsc --noEmit 2>&1 | head -20 || true"
          }
        ]
      }
    ]
  }
}
```

---

### 8. テストファイル変更後に自動でテスト実行

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "FILE=\"$CLAUDE_TOOL_OUTPUT_FILE_PATH\"; [[ \"$FILE\" == *.test.* || \"$FILE\" == *.spec.* ]] && npm test -- --testPathPattern=\"$FILE\" 2>&1 | tail -20 || true"
          }
        ]
      }
    ]
  }
}
```

---

## フック内で使える環境変数

| 変数名 | 説明 |
|-------|------|
| `$CLAUDE_TOOL_NAME` | 実行されたツール名（例：`Bash`, `Write`） |
| `$CLAUDE_TOOL_INPUT` | ツールへの入力内容 |
| `$CLAUDE_TOOL_OUTPUT` | ツールの出力内容（PostToolUse のみ） |
| `$CLAUDE_TOOL_OUTPUT_FILE_PATH` | 書き込まれたファイルパス（Write/Edit のみ） |

---

## フックの終了コード

| 終了コード | 意味 |
|----------|------|
| `0` | 成功。処理を続ける |
| `1` | 失敗。PreToolUse の場合はツール実行をブロック |

```bash
# ブロックする例
exit 1

# 続行する例
exit 0
```

---

## 複数のフックを組み合わせる

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "npx prettier --write \"$CLAUDE_TOOL_OUTPUT_FILE_PATH\" 2>/dev/null || true"
          },
          {
            "type": "command",
            "command": "npx eslint --fix \"$CLAUDE_TOOL_OUTPUT_FILE_PATH\" 2>/dev/null || true"
          },
          {
            "type": "command",
            "command": "echo \"[$(date '+%Y-%m-%d %H:%M:%S')] 編集: $CLAUDE_TOOL_OUTPUT_FILE_PATH\" >> ~/.claude/edit-history.log"
          }
        ]
      }
    ]
  }
}
```

---

## フックのデバッグ

フックが正しく動いているか確認する方法：

```bash
# ログファイルを確認
tail -f ~/.claude/command-history.log

# フックスクリプトを直接テストする
CLAUDE_TOOL_OUTPUT_FILE_PATH="src/index.ts" bash -c 'npx prettier --write "$CLAUDE_TOOL_OUTPUT_FILE_PATH"'
```

---

## 注意事項

- フックのコマンドは **シェルで実行される** ため、セキュリティに注意してください
- `PreToolUse` で `exit 1` すると Claude Code の作業がブロックされます
- フックが重くなると Claude Code 全体の動作が遅くなります。軽い処理に留めましょう
- 無限ループに注意（フックがファイルを書き込み → また PostToolUse が発火）
