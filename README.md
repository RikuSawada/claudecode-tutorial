# Claude Code チュートリアル

Claude Code をこれから使い始める人のための入門ガイドです。
インストールから実践的な活用方法まで、すぐに使えるようになることを目的としています。

---

## ドキュメント一覧

### 入門
| ドキュメント | 内容 |
|------------|------|
| [Getting Started](docs/getting-started.md) | インストール・起動・初回セットアップ |
| [基本的な使い方](docs/basic-usage.md) | 会話の仕方・よくあるユースケース・精度を上げるコツ |
| [コマンドリファレンス](docs/commands.md) | CLI コマンド・スラッシュコマンド・キーボードショートカット一覧 |

### 活用
| ドキュメント | 内容 |
|------------|------|
| [開発ワークフロー](docs/workflow.md) | 要件定義・設計・製造・UT・IT・ST・リリースでの活用 |
| [課題・TODO 管理](docs/task-management.md) | ローカルファイル・GitHub Issues・Notion など管理パターン別の活用方法 |
| [Skills & スラッシュコマンド](docs/skills.md) | 組み込みコマンドとカスタムスキルの作り方 |
| [GitHub 連携](docs/github.md) | Issue・PR の操作・典型的な開発フロー |
| [CLAUDE.md の活用](docs/claude-md.md) | プロジェクト固有の設定ファイルの書き方 |
| [Memory（記憶機能）](docs/memory.md) | セッションをまたいだ記憶の活用方法 |
| [ベストプラクティス](docs/best-practices.md) | 公式＋実践知を統合した運用ナレッジ（検証・コンテキスト管理・自動化） |

### 高度な設定
| ドキュメント | 内容 |
|------------|------|
| [MCP（外部ツール連携）](docs/mcp.md) | DB・Slack・ブラウザなど外部サービスとの連携 |
| [Hooks（フック設定）](docs/hooks.md) | ツール実行前後に自動でスクリプトを走らせる |
| [権限・安全設定](docs/permissions.md) | 操作の承認ルール・機密情報の取り扱い |
| [Codex との連携](docs/codex.md) | OpenAI Codex CLI との使い分け・組み合わせ方 |

---

## Claude Code とは

Claude Code は Anthropic が提供する **ターミナルで動く AI コーディングアシスタント** です。

- 自然言語でコードの説明・修正・実装を依頼できる
- ファイルの読み書き・コマンド実行をサポート
- GitHub と連携して Issue・PR を操作できる
- プロジェクトごとにカスタマイズ可能

---

## クイックスタート

```bash
# インストール
npm install -g @anthropic-ai/claude-code

# プロジェクトに移動して起動
cd your-project
claude
```

詳細は [Getting Started](docs/getting-started.md) を参照してください。

---

## はじめての方へ

**まずはここから：**

1. **[Getting Started](docs/getting-started.md)** でセットアップを完了させる
2. **[基本的な使い方](docs/basic-usage.md)** で会話のコツを掴む
3. **[ベストプラクティス](docs/best-practices.md)** で効果的な使い方を学ぶ

**慣れてきたら：**

4. **[CLAUDE.md の活用](docs/claude-md.md)** でプロジェクトを設定する
5. **[Memory](docs/memory.md)** でセッションをまたいだ記憶を活用する
6. **[GitHub 連携](docs/github.md)** で開発フローに組み込む
7. **[Skills](docs/skills.md)** で繰り返し作業を自動化する

**さらに使いこなす：**

8. **[MCP](docs/mcp.md)** で外部サービスと連携する
9. **[Hooks](docs/hooks.md)** でツール実行を自動化する
10. **[権限・安全設定](docs/permissions.md)** でセキュアな環境を整える

---

## 参考リンク

- [Claude Code 公式ドキュメント](https://docs.anthropic.com/claude-code)
- [GitHub Issues（不具合報告・要望）](https://github.com/anthropics/claude-code/issues)
- [効果的な CLAUDE.md の書き方](https://zenn.dev/farstep/articles/how-to-write-a-great-claude-md)
- [個人的 CLAUDE.md のすゝめ](https://zenn.dev/solvio/articles/b53d24a41e6d5b)
