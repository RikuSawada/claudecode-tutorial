# API エンドポイント・関数命名規則

Claude Code が FastAPI エンドポイントや Python 関数を実装する際に従うルール。

---

## エンドポイント命名

### URL 設計

- リソースは複数形の名詞で表す
- 動詞は HTTP メソッドで表現し、URL に含めない
- パスパラメータはリソース ID を表す場合のみ使う

```
# Good
GET    /tasks              # 一覧取得
POST   /tasks              # 作成
GET    /tasks/{task_id}    # 詳細取得
PATCH  /tasks/{task_id}    # 部分更新
DELETE /tasks/{task_id}    # 削除

# Bad
GET  /get_tasks
POST /create_task
GET  /task_detail?id=1
```

### HTMX パーシャル用エンドポイント

HTMX が差し替えるパーシャル HTML を返すエンドポイントは `/partials/` プレフィックスを付ける。

```
GET /partials/tasks/list         # タスク一覧のパーシャル
GET /partials/tasks/{task_id}/row  # タスク行のパーシャル
```

---

## Python 関数命名

### ルーター関数（エンドポイントハンドラ）

`{動詞}_{リソース}` の形式。

```python
# Good
async def get_tasks(...):      # 一覧取得
async def create_task(...):    # 作成
async def get_task(...):       # 詳細取得
async def update_task(...):    # 更新
async def delete_task(...):    # 削除

# Bad
async def tasks_list(...):
async def task_create(...):
```

### CRUD 関数（`crud/` 配下）

ルーター関数と同じ命名に `db_` プレフィックスは付けない。第一引数は必ず `db: AsyncSession`。

```python
# crud/tasks.py
async def get_tasks(db: AsyncSession, user_id: int) -> list[Task]: ...
async def create_task(db: AsyncSession, data: TaskCreate) -> Task: ...
async def get_task(db: AsyncSession, task_id: int) -> Task | None: ...
async def update_task(db: AsyncSession, task_id: int, data: TaskUpdate) -> Task: ...
async def delete_task(db: AsyncSession, task_id: int) -> None: ...
```

---

## Pydantic スキーマ命名

| 用途 | 命名規則 | 例 |
|------|----------|-----|
| 作成リクエスト | `{Model}Create` | `TaskCreate` |
| 更新リクエスト | `{Model}Update` | `TaskUpdate` |
| レスポンス | `{Model}Response` | `TaskResponse` |
| DB モデルそのもの | `{Model}` | `Task` |

```python
# schemas/task.py
class TaskCreate(BaseModel):
    title: str
    description: str | None = None

class TaskUpdate(BaseModel):
    title: str | None = None
    description: str | None = None
    is_done: bool | None = None

class TaskResponse(BaseModel):
    id: int
    title: str
    description: str | None
    is_done: bool
    created_at: datetime

    model_config = ConfigDict(from_attributes=True)
```

---

## SQLAlchemy モデル命名

- クラス名は単数形 PascalCase（例: `Task`、`User`）
- テーブル名は複数形 snake_case（`__tablename__ = "tasks"`）
- カラム名は snake_case（例: `created_at`、`is_done`）
- 外部キーは `{参照テーブル単数形}_id`（例: `user_id`）

```python
class Task(Base):
    __tablename__ = "tasks"

    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str] = mapped_column(String(255))
    is_done: Mapped[bool] = mapped_column(default=False)
    user_id: Mapped[int] = mapped_column(ForeignKey("users.id"))
    created_at: Mapped[datetime] = mapped_column(default=func.now())
```

---

## やってはいけないこと

- URL に動詞を含める（`/get_task`、`/delete_task` など）
- スキーマを作成・更新・レスポンスで使い回す（用途ごとに分ける）
- ルーターに DB 操作を直接書く（必ず `crud/` に切り出す）
- 型ヒントなしで関数を定義する
