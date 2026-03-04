# リポジトリ構成テンプレート

---

## 推奨ディレクトリ構成

```
project-root/
├── .github/
│   ├── CODEOWNERS                  ← 重要領域のレビュー必須化
│   ├── ISSUE_TEMPLATE/
│   │   ├── feature.md
│   │   └── bug_report.md
│   ├── pull_request_template.md
│   └── workflows/
│       ├── ci.yml                  ← lint/test/coverage
│       └── release.yml             ← タグ push 時のリリース自動化
│
├── docs/
│   ├── adr/                        ← アーキテクチャ決定記録
│   │   └── 0001-{タイトル}.md
│   ├── api/                        ← API 設計書
│   ├── db/                         ← DB スキーマ定義
│   ├── design/                     ← 機能設計書
│   └── prompts/                    ← AIプロンプト資産
│       ├── requirements.md
│       ├── design.md
│       ├── implementation.md
│       ├── review.md
│       └── debug.md
│
├── src/
│   ├── domain/                     ← ドメインロジック（根幹領域）
│   ├── application/                ← ユースケース
│   ├── infrastructure/             ← DB・外部API・メール等の実装
│   ├── presentation/               ← API コントローラ・画面
│   └── shared/                     ← 共通ユーティリティ・型定義
│
├── tests/
│   ├── unit/                       ← UT（src と同じ構造）
│   ├── integration/                ← IT（API〜DB）
│   └── e2e/                        ← E2E（Playwright等）
│
├── CLAUDE.md                       ← AI 用コンテキスト（必須）
├── CONTRIBUTING.md                 ← 開発参加ガイド
├── CHANGELOG.md                    ← 変更履歴（自動生成）
└── README.md
```

---

## 命名規約

### ブランチ名

```
feature/issue-{番号}-{概要}     # 機能追加
fix/issue-{番号}-{概要}         # バグ修正
refactor/{概要}                 # リファクタリング
chore/{概要}                    # 依存更新・CI設定変更
```

例：`feature/issue-42-email-notification`

### コミットメッセージ（Conventional Commits）

```
{タイプ}({スコープ}): {概要}

タイプ一覧：
  feat:     機能追加
  fix:      バグ修正
  refactor: 振る舞いを変えないリファクタリング
  test:     テスト追加・修正
  docs:     ドキュメントのみの変更
  chore:    ビルド・CI・依存更新
  perf:     パフォーマンス改善
```

例：
```
feat(auth): JWTリフレッシュトークンの自動更新を追加
fix(payment): 決済金額の計算が端数で丸め誤差になる問題を修正
```

---

## コーディング規約

プロジェクトの技術スタックに合わせて選択する。以下は共通の方針。

### フォーマッター・リンター

| ツール | 対象 | 設定ファイル |
|--------|------|------------|
| Prettier | JS/TS/JSON/YAML | `.prettierrc` |
| ESLint | JS/TS | `.eslintrc` |
| Ruff | Python | `ruff.toml` |
| gofmt | Go | （標準） |

- フォーマットは CI で自動チェック（差分があれば fail）
- コミット前に自動フォーマットされるよう pre-commit hook を設定する

### 型・例外・ログの方針

**型**
- 暗黙の `any` 禁止（TypeScript の場合は `strict: true`）
- 外部 API のレスポンスは型定義を持つ（Zod/io-ts 等でバリデーション）

**例外処理**
- 例外の握りつぶし禁止（`catch {}` で何もしない）
- 業務例外とシステム例外を分ける
- ユーザーに返すエラーメッセージに内部情報を含めない

**ログ**
- ログレベル：`debug`（開発）/ `info`（業務イベント）/ `warn`（注意）/ `error`（障害）
- 個人情報・秘密情報をログに出力しない
- トレース ID を含める（リクエスト追跡）

---

## CI 設定（最小構成）

以下を `.github/workflows/ci.yml` に配置する。

```yaml
name: CI

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run lint
      - run: npm run typecheck

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run test:coverage
      - name: カバレッジレポートを PR にコメント
        # Vitest を使う場合。Jest の場合は ArtiomTr/jest-coverage-report-action 等に差し替える
        uses: davelosert/vitest-coverage-report-action@v2

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: 依存関係の脆弱性スキャン
        run: npm audit --audit-level=high
      - name: secrets のスキャン
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
```

---

## CODEOWNERS

以下を `.github/CODEOWNERS` に配置する。

```
# デフォルト：変更があれば全員に通知
*                       @team-lead

# セキュリティ・認証
/src/auth/              @security-reviewer @team-lead
/src/middleware/auth*   @security-reviewer @team-lead

# 決済・課金
/src/payment/           @team-lead
/src/billing/           @team-lead

# インフラ・CI
/.github/               @infra-team @team-lead
/infrastructure/        @infra-team @team-lead
/terraform/             @infra-team @team-lead

# DB マイグレーション
/migrations/            @team-lead
/db/                    @team-lead
```

`@team-lead` の部分はGitHubのユーザー名またはチーム名（`@org/team-name`）に変更する。

---

## CONTRIBUTING.md テンプレート

以下を `CONTRIBUTING.md` に配置する。

```markdown
# 開発への参加ガイド

## セットアップ

1. リポジトリをクローン
2. 依存関係をインストール：`npm install`
3. 環境変数を設定：`.env.example` を `.env.local` にコピーして編集
4. ローカル起動：`npm run dev`

## 開発の進め方

1. Issue を確認して担当をアサインする
2. `feature/issue-{番号}-{概要}` ブランチを切る
3. 実装・テスト
4. `npm test` で全テスト通過を確認
5. PR を作成（テンプレートに従う）

## AI ツールの使い方

- Claude Code：計画・設計・PR説明文の生成
- Codex：実装・テスト実装
- 詳細は [ai-dev-playbook/](ai-dev-playbook/) を参照

## コミットメッセージ

Conventional Commits に従う（→ [repo-template.md](ai-dev-playbook/repo-template.md)）

## レビュー

- PR 作成後 1営業日以内にレビューを開始する
- セキュリティ・根幹領域のチェックリストを必ず確認する
- 承認は最低1名（CODEOWNERS 対象領域は指定レビュアーの承認が必須）
```

---

## ADR テンプレート

以下を `docs/adr/0001-{タイトル}.md` の形式で作成する。

```markdown
# ADR-{番号}：{タイトル}

- **日付**：YYYY-MM-DD
- **ステータス**：提案中 / 承認済み / 廃止

## コンテキスト

<!-- なぜこの決定が必要になったか -->

## 決定

<!-- 何を選択したか -->

## 根拠

<!-- なぜこれを選んだか。比較した選択肢と理由 -->

| 選択肢 | メリット | デメリット |
|--------|---------|---------|
| A（選択） | | |
| B | | |

## 結果

<!-- この決定によって生じること。メリット・デメリット・注意点 -->

## 参考

-
```
