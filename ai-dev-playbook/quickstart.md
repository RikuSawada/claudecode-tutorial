# クイックスタートガイド

このフローを30分で体験し、チームに展開するための手順書。
概念を読む前に手を動かしたい人はここから始める。

---

## Part 1：個人で体験する（約30分）

### 前提

| ツール | インストール | 確認 |
|--------|------------|------|
| Claude Code | `npm install -g @anthropic-ai/claude-code` | `claude --version` |
| Codex CLI | `npm install -g @openai/codex` | `codex --version` |
| Git / GitHub | — | `git --version` |

初回ログイン：

```bash
claude   # ブラウザが開く。Anthropicアカウントでログイン
```

---

### Step 1：プロジェクトを用意する

既存のGitHubリポジトリで試す場合はそのままで構わない。
新規の場合：

```bash
mkdir my-project && cd my-project
git init
echo "# My Project" > README.md
git add . && git commit -m "Initial commit"
```

---

### Step 2：Claude CodeでIssueを作る

```bash
cd my-project
claude
```

以下を貼り付けて試す：

```
ユーザーがメールアドレスを入力するフォームを追加したい。
このリポジトリの状況を確認して、GitHub Issue の下書きを作って。
以下の項目を含めること：
- 背景・目的
- スコープ（やること/やらないこと）
- 受入条件（AC）
- 影響範囲（API/DB/画面）
- テスト観点（UT/IT/E2E）
```

生成されたIssueを**自分で確認してから**GitHubに登録する。
ACが具体的か、スコープが広すぎないかを必ずチェックすること。

---

### Step 3：Codexで実装する

```bash
git checkout -b feature/email-form
codex "メールアドレス入力フォームを実装して。形式チェック・空欄チェックのバリデーションも含めること。"
```

Codexは変更前に確認を求める。**提案内容を読んでから**承認する。

```bash
npm test   # テストが通ることを確認
git add . && git commit -m "feat: メールアドレス入力フォームを追加"
```

---

### Step 4：Claude CodeでPR説明文を生成する

```bash
claude
```

```
git diff main との差分を確認して、PRの説明文を作って。
pr-template.md の形式に従うこと（目的・変更点・テスト・リスク・ロールバック手順）。
人間が特に確認すべき箇所も最後に列挙して。
```

生成された説明文を確認してからGitHubのPRに貼り付ける。

---

### Part 1 チェックリスト

- [ ] Claude Code を使ってIssueの下書きを作れた
- [ ] Codex で実装してテストが通った
- [ ] Claude Code でPR説明文を生成できた

---

## Part 2：チームに展開する（約1時間）

### 展開前に決めること（必須）

着手前にチームで合意する。決めないまま進めると後でブレる。

| 項目 | 内容 |
|------|------|
| AIが書き込めるブランチ | `feature/*` のみを推奨。mainへの直接pushは禁止 |
| AIが触ってはいけない領域 | `src/auth/`・`src/payment/`・本番設定ファイル など |
| 人間レビューが必須な変更 | セキュリティ・根幹ロジック・スキーマ変更（→ [ai-usage-guidelines.md](ai-usage-guidelines.md)） |

---

### Step 1：GitHubテンプレートを追加する

```bash
mkdir -p .github/ISSUE_TEMPLATE
```

- `.github/pull_request_template.md` → [pr-template.md](pr-template.md) のテンプレートをコピー
- `.github/ISSUE_TEMPLATE/feature.md` → [issue-template.md](issue-template.md) のテンプレートをコピー
- `.github/CODEOWNERS` → [repo-template.md](repo-template.md) の CODEOWNERS をコピーして担当者名を変更

---

### Step 2：CLAUDE.md をリポジトリに追加する

AIにプロジェクトのルールを伝えるファイル。これがないとAIがプロジェクト固有の規約を無視する。

```bash
claude
```

```
このリポジトリの構成を分析して、CLAUDE.md の叩き台を作って。
以下の項目を含めること：
- プロジェクト概要と技術スタック
- ディレクトリ構成と各ディレクトリの役割
- コーディング規約（既存コードから読み取れるもの）
- AIへの禁止事項（触ってはいけないファイル・領域）
推測で書かずに、確認できる情報だけ記載して。不明な点は「要確認」と明記。
```

チームでレビューして合意した内容に修正してからコミットする。

---

### Step 3：CI を整備する

[repo-template.md](repo-template.md) の CI 設定を `.github/workflows/ci.yml` に追加する。
最低限 `lint` / `test` / `coverage` が PR 時に自動実行される状態にする。

---

### Step 4：チームに案内する

展開前に以下を共有する：

1. **AIの役割と権限** — 何をAIに任せて、何は人間がやるか（→ [development-flow.md](development-flow.md)）
2. **レビュー必須ポイント** — [ai-usage-guidelines.md](ai-usage-guidelines.md) のチェックリストを全員が把握する
3. **Part 1 をチーム全員で実施** — 概念の説明だけでなく、手を動かす場を設ける

> 最初の1〜2週間はチームリーダーがAIの変更内容を毎回確認する体制を作ること。
> ルールを決めずに全員に使わせると、根幹ロジックの書き換えや secrets 漏洩などの事故が起きやすい。

---

## よくある失敗

| 失敗 | 対策 |
|------|------|
| AIがドメインルールを無視した実装をする | CLAUDE.md にルール・禁止事項を記載する |
| 生成されたテストが「通るだけ」で意味がない | レビューで「何を検証しているか」を必ず確認する |
| PRが大きすぎてレビューできない | タスクを小さく分割してから Codex に渡す |
| secrets がコミットに混入する | `.gitignore` + pre-commit hook（git-secrets等）で防ぐ |

---

## 次のステップ

- [development-flow.md](development-flow.md) — 全工程のフローと役割分担の詳細
- [ai-usage-guidelines.md](ai-usage-guidelines.md) — AIへの禁止領域・コンテキスト設計
- [prompt-template.md](prompt-template.md) — 工程別のプロンプトテンプレート集
