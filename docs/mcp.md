# MCP（Model Context Protocol）

---

## MCP とは

MCP（Model Context Protocol）は、Claude Code に **外部ツールやサービスを接続する仕組み** です。

通常の Claude Code はローカルファイルやターミナルを操作できますが、
MCP を使うと以下のような外部サービスも操作できるようになります：

- データベース（PostgreSQL、MySQL、SQLite）
- ブラウザ（Webページの取得・操作）
- Slack、Notion、GitHub（API経由）
- 社内ツール・カスタムサービス

---

## MCP サーバーの仕組み

```
Claude Code  ←→  MCP サーバー  ←→  外部サービス
```

MCP サーバーは Claude Code と外部サービスの仲介役です。
サーバーを起動しておくことで、Claude Code がそのサービスのツールを呼び出せます。

---

## セットアップ方法

### 設定ファイルの場所

MCP の設定は以下のいずれかに記述します：

| 設定ファイル | 有効範囲 |
|------------|---------|
| `~/.claude/settings.json` | 全プロジェクト共通 |
| `your-project/.claude/settings.json` | そのプロジェクトのみ |

### 設定の基本形式

```json
{
  "mcpServers": {
    "サーバー名": {
      "command": "起動コマンド",
      "args": ["引数1", "引数2"],
      "env": {
        "環境変数名": "値"
      }
    }
  }
}
```

---

## よく使う MCP サーバー

### 1. ブラウザ操作（Puppeteer）

Web ページを取得・操作できます。

```bash
npm install -g @modelcontextprotocol/server-puppeteer
```

```json
{
  "mcpServers": {
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"]
    }
  }
}
```

使い方：
```
「https://example.com のページを開いてスクリーンショットを撮って」
「このサイトのログインフォームに入力してログインして」
```

---

### 2. ファイルシステム拡張

指定ディレクトリへのアクセスを追加できます。

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/Users/yourname/Documents",
        "/Users/yourname/Desktop"
      ]
    }
  }
}
```

---

### 3. PostgreSQL データベース

DB に自然言語でクエリを実行できます。

```bash
npm install -g @modelcontextprotocol/server-postgres
```

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://user:password@localhost:5432/mydb"
      }
    }
  }
}
```

使い方：
```
「usersテーブルの構造を確認して」
「先月の売上上位10件を集計して」
「注文ステータスが 'pending' のレコードを全件取得して」
```

---

### 4. SQLite

ローカルの SQLite ファイルに接続できます。

```json
{
  "mcpServers": {
    "sqlite": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-sqlite",
        "--db-path",
        "./data/myapp.db"
      ]
    }
  }
}
```

---

### 5. Slack

Slack にメッセージを送ったりチャンネルを読んだりできます。

```json
{
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-your-token",
        "SLACK_TEAM_ID": "T0000000000"
      }
    }
  }
}
```

使い方：
```
「#general チャンネルに今日のリリース内容を投稿して」
「#dev チャンネルの直近のメッセージを確認して」
```

---

### 6. GitHub（拡張）

標準の gh コマンドに加えて、より詳細な GitHub 操作が可能になります。

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_your_token"
      }
    }
  }
}
```

---

## カスタム MCP サーバーを作る

独自のサービスを接続したい場合は、MCP サーバーを自作できます。

### Node.js での実装例

```bash
npm install @modelcontextprotocol/sdk
```

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { CallToolRequestSchema, ListToolsRequestSchema } from "@modelcontextprotocol/sdk/types.js";

const server = new Server(
  { name: "my-custom-server", version: "1.0.0" },
  { capabilities: { tools: {} } }
);

// ツールの定義
server.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: [
    {
      name: "get_weather",
      description: "指定した都市の天気を取得する",
      inputSchema: {
        type: "object",
        properties: {
          city: { type: "string", description: "都市名" }
        },
        required: ["city"]
      }
    }
  ]
}));

// ツールの実装
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  if (request.params.name === "get_weather") {
    const city = request.params.arguments?.city;
    // 実際の天気取得処理
    return {
      content: [{ type: "text", text: `${city}の天気: 晴れ、25°C` }]
    };
  }
  throw new Error("Unknown tool");
});

// サーバー起動
const transport = new StdioServerTransport();
await server.connect(transport);
```

設定：

```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["./mcp-server/index.js"]
    }
  }
}
```

---

## MCP サーバーの確認

設定が正しく読み込まれているか確認：

```
「利用可能なツールを一覧表示して」
「どのMCPサーバーが接続されているか教えて」
```

---

## トラブルシューティング

| 症状 | 原因と対処 |
|-----|----------|
| MCP が認識されない | 設定ファイルの JSON が正しいか確認。Claude Code を再起動 |
| 接続エラー | `command` のパス・`args` が正しいか確認。`npx` が使える環境か確認 |
| 権限エラー | 環境変数（APIキー等）が正しく設定されているか確認 |
| タイムアウト | MCP サーバーの起動に時間がかかっている。ログを確認 |

---

## 参考

- [MCP 公式ドキュメント](https://modelcontextprotocol.io)
- [MCP サーバー一覧（公式）](https://github.com/modelcontextprotocol/servers)
