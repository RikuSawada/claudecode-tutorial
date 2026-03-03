# Getting Started - セットアップガイド

---

## 必要な環境

- macOS / Linux / Windows (WSL)
- Node.js 18 以上
- npm

Node.js がインストールされているか確認：

```bash
node --version  # v18.0.0 以上であればOK
npm --version
```

---

## インストール

```bash
npm install -g @anthropic-ai/claude-code
```

インストールが完了したか確認：

```bash
claude --version
```

バージョン番号が表示されれば成功です。

---

## 起動とログイン

### 1. プロジェクトのディレクトリに移動する

```bash
cd your-project
```

### 2. Claude Code を起動する

```bash
claude
```

### 3. ブラウザでログインする

初回起動時に以下のメッセージが表示され、ブラウザが自動で開きます：

```
Opening browser to authenticate...
```

Anthropic のアカウントでログインしてください（アカウントがない場合は[こちらで作成](https://console.anthropic.com)）。

### 4. ターミナルに戻る

ログインが完了すると、ターミナルに戻り以下のような入力プロンプトが表示されます：

```
>
```

これで使用開始できます。

---

## はじめての会話

起動したら、まず試してみましょう：

```
「このプロジェクトの構成を説明して」
```

または、シンプルな質問でもOKです：

```
「Pythonでフィボナッチ数列を返す関数を書いて」
```

Claude Code がファイルを読んで応答してくれます。

---

## よくあるエラーと対処法

### `command not found: claude`

インストールが正しくできていない可能性があります。

```bash
# npm のグローバルパスを確認
npm config get prefix

# パスが通っていない場合は .zshrc / .bashrc に追記
export PATH="$(npm config get prefix)/bin:$PATH"
source ~/.zshrc
```

### ブラウザが開かない

以下のコマンドで手動認証できます：

```bash
claude auth login
```

### `Error: API rate limit exceeded`

API の利用制限に達しています。しばらく待ってから再試行してください。

---

## 次のステップ

セットアップが完了したら、次のドキュメントに進みましょう：

- [基本的な使い方](basic-usage.md) - 会話の仕方とよく使う依頼パターン
- [CLAUDE.md の活用](claude-md.md) - プロジェクトに設定ファイルを作る
