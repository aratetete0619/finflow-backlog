# FinFlow: テーブル定義

## USERS

| カラム名 | データ型 | 制約 | 説明 |
|----------|----------|------|------|
| id | UUID | PRIMARY KEY | ユーザーの一意識別子 |
| email | VARCHAR(255) | UNIQUE, NOT NULL | ユーザーのメールアドレス |
| password_hash | VARCHAR(255) | NOT NULL | ハッシュ化されたパスワード |
| first_name | VARCHAR(100) | | ユーザーの名 |
| last_name | VARCHAR(100) | | ユーザーの姓 |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |

## ACCOUNTS

| カラム名 | データ型 | 制約 | 説明 |
|----------|----------|------|------|
| id | UUID | PRIMARY KEY | アカウントの一意識別子 |
| user_id | UUID | FOREIGN KEY (USERS.id) | 所有ユーザーのID |
| name | VARCHAR(100) | NOT NULL | アカウント名 |
| type | VARCHAR(50) | NOT NULL | アカウントタイプ (例: 普通預金、クレジットカード) |
| balance | DECIMAL(15,2) | NOT NULL | 現在の残高 |
| currency | VARCHAR(3) | NOT NULL | 通貨コード (例: JPY, USD) |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |

## CATEGORIES

| カラム名 | データ型 | 制約 | 説明 |
|----------|----------|------|------|
| id | UUID | PRIMARY KEY | カテゴリの一意識別子 |
| name | VARCHAR(100) | NOT NULL | カテゴリ名 |
| type | VARCHAR(10) | NOT NULL, CHECK (type IN ('income', 'expense')) | カテゴリタイプ |
| user_id | UUID | FOREIGN KEY (USERS.id) | カスタムカテゴリの場合の所有ユーザーID |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |

## TRANSACTIONS

| カラム名 | データ型 | 制約 | 説明 |
|----------|----------|------|------|
| id | UUID | PRIMARY KEY | 取引の一意識別子 |
| user_id | UUID | FOREIGN KEY (USERS.id) | 取引所有者のユーザーID |
| account_id | UUID | FOREIGN KEY (ACCOUNTS.id) | 関連するアカウントのID |
| category_id | UUID | FOREIGN KEY (CATEGORIES.id) | 関連するカテゴリのID |
| amount | DECIMAL(15,2) | NOT NULL | 取引金額 |
| description | TEXT | | 取引の説明 |
| date | DATE | NOT NULL | 取引日 |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |

## BUDGETS

| カラム名 | データ型 | 制約 | 説明 |
|----------|----------|------|------|
| id | UUID | PRIMARY KEY | 予算の一意識別子 |
| user_id | UUID | FOREIGN KEY (USERS.id) | 予算所有者のユーザーID |
| category_id | UUID | FOREIGN KEY (CATEGORIES.id) | 関連するカテゴリのID |
| amount | DECIMAL(15,2) | NOT NULL | 予算額 |
| start_date | DATE | NOT NULL | 予算期間の開始日 |
| end_date | DATE | NOT NULL | 予算期間の終了日 |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |

## FINANCIAL_GOALS

| カラム名 | データ型 | 制約 | 説明 |
|----------|----------|------|------|
| id | UUID | PRIMARY KEY | 財務目標の一意識別子 |
| user_id | UUID | FOREIGN KEY (USERS.id) | 目標所有者のユーザーID |
| name | VARCHAR(255) | NOT NULL | 目標の名称 |
| target_amount | DECIMAL(15,2) | NOT NULL | 目標金額 |
| current_amount | DECIMAL(15,2) | NOT NULL, DEFAULT 0 | 現在の積立額 |
| deadline | DATE | | 目標期限 |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |

## INVESTMENTS

| カラム名 | データ型 | 制約 | 説明 |
|----------|----------|------|------|
| id | UUID | PRIMARY KEY | 投資の一意識別子 |
| user_id | UUID | FOREIGN KEY (USERS.id) | 投資所有者のユーザーID |
| name | VARCHAR(255) | NOT NULL | 投資の名称 |
| type | VARCHAR(50) | NOT NULL | 投資タイプ (例: 株式、債券、投資信託) |
| amount | DECIMAL(15,2) | NOT NULL | 投資金額 |
| purchase_date | DATE | NOT NULL | 購入日 |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |

## NOTIFICATIONS

| カラム名 | データ型 | 制約 | 説明 |
|----------|----------|------|------|
| id | UUID | PRIMARY KEY | 通知の一意識別子 |
| user_id | UUID | FOREIGN KEY (USERS.id) | 通知対象のユーザーID |
| type | VARCHAR(50) | NOT NULL | 通知タイプ |
| message | TEXT | NOT NULL | 通知メッセージ |
| is_read | BOOLEAN | NOT NULL, DEFAULT FALSE | 既読フラグ |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |

## USER_SETTINGS

| カラム名 | データ型 | 制約 | 説明 |
|----------|----------|------|------|
| user_id | UUID | PRIMARY KEY, FOREIGN KEY (USERS.id) | 設定所有者のユーザーID |
| default_currency | VARCHAR(3) | NOT NULL, DEFAULT 'JPY' | デフォルト通貨 |
| notification_preferences | JSONB | NOT NULL, DEFAULT '{}' | 通知設定 |
| theme | VARCHAR(20) | DEFAULT 'light' | UIテーマ設定 |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |


# ER図
```mermaid
erDiagram
    USERS ||--o{ ACCOUNTS : has
    USERS ||--o{ TRANSACTIONS : makes
    USERS ||--o{ BUDGETS : sets
    USERS ||--o{ FINANCIAL_GOALS : sets
    USERS ||--o{ INVESTMENTS : owns
    USERS ||--o{ NOTIFICATIONS : receives
    USERS ||--|| USER_SETTINGS : has

    ACCOUNTS ||--o{ TRANSACTIONS : contains

    CATEGORIES ||--o{ TRANSACTIONS : categorizes
    CATEGORIES ||--o{ BUDGETS : applied_to

    USERS {
        uuid id PK
        string email UK
        string password_hash
        string first_name
        string last_name
        datetime created_at
        datetime updated_at
    }

    ACCOUNTS {
        uuid id PK
        uuid user_id FK
        string name
        string type
        decimal balance
        string currency
        datetime created_at
        datetime updated_at
    }

    CATEGORIES {
        uuid id PK
        string name
        string type
        uuid user_id FK "NULL for system categories"
        datetime created_at
    }

    TRANSACTIONS {
        uuid id PK
        uuid user_id FK
        uuid account_id FK
        uuid category_id FK
        decimal amount
        string description
        date date
        datetime created_at
        datetime updated_at
    }

    BUDGETS {
        uuid id PK
        uuid user_id FK
        uuid category_id FK
        decimal amount
        date start_date
        date end_date
        datetime created_at
        datetime updated_at
    }

    FINANCIAL_GOALS {
        uuid id PK
        uuid user_id FK
        string name
        decimal target_amount
        decimal current_amount
        date deadline
        datetime created_at
        datetime updated_at
    }

    INVESTMENTS {
        uuid id PK
        uuid user_id FK
        string name
        string type
        decimal amount
        date purchase_date
        datetime created_at
        datetime updated_at
    }

    NOTIFICATIONS {
        uuid id PK
        uuid user_id FK
        string type
        string message
        boolean is_read
        datetime created_at
    }

    USER_SETTINGS {
        uuid user_id PK, FK
        string default_currency
        json notification_preferences
        string theme
        datetime updated_at
    }
  ```


# FinFlow: インデックス戦略

## USERS テーブル
1. email (UNIQUE)
   - 理由: ログイン時のユーザー検索で使用

## ACCOUNTS テーブル
1. user_id
   - 理由: ユーザーごとのアカウント一覧表示で使用

## CATEGORIES テーブル
1. (user_id, type)
   - 理由: ユーザーごとのカテゴリ一覧表示（収入/支出別）で使用

## TRANSACTIONS テーブル
1. (user_id, date)
   - 理由: ユーザーごとの取引履歴表示（日付順）で使用
2. (account_id, date)
   - 理由: アカウントごとの取引履歴表示で使用
3. (category_id, date)
   - 理由: カテゴリごとの取引集計で使用
4. (user_id, category_id, date)
   - 理由: ユーザーごとのカテゴリ別取引集計で使用

## BUDGETS テーブル
1. (user_id, start_date, end_date)
   - 理由: ユーザーごとの予算一覧表示（期間指定）で使用
2. (category_id, start_date, end_date)
   - 理由: カテゴリごとの予算追跡で使用

## FINANCIAL_GOALS テーブル
1. (user_id, deadline)
   - 理由: ユーザーごとの財務目標一覧表示（期限順）で使用

## INVESTMENTS テーブル
1. (user_id, type)
   - 理由: ユーザーごとの投資一覧表示（タイプ別）で使用
2. (user_id, purchase_date)
   - 理由: ユーザーごとの投資履歴表示（日付順）で使用

## NOTIFICATIONS テーブル
1. (user_id, created_at)
   - 理由: ユーザーごとの通知一覧表示（最新順）で使用
2. (user_id, is_read, created_at)
   - 理由: 未読通知のフィルタリングで使用

## USER_SETTINGS テーブル
- プライマリキー (user_id) のみで十分（小規模テーブル）

## インデックス作成の SQL 例

```sql
-- USERS テーブル
CREATE UNIQUE INDEX idx_users_email ON users(email);

-- ACCOUNTS テーブル
CREATE INDEX idx_accounts_user_id ON accounts(user_id);

-- CATEGORIES テーブル
CREATE INDEX idx_categories_user_id_type ON categories(user_id, type);

-- TRANSACTIONS テーブル
CREATE INDEX idx_transactions_user_id_date ON transactions(user_id, date);
CREATE INDEX idx_transactions_account_id_date ON transactions(account_id, date);
CREATE INDEX idx_transactions_category_id_date ON transactions(category_id, date);
CREATE INDEX idx_transactions_user_id_category_id_date ON transactions(user_id, category_id, date);

-- BUDGETS テーブル
CREATE INDEX idx_budgets_user_id_dates ON budgets(user_id, start_date, end_date);
CREATE INDEX idx_budgets_category_id_dates ON budgets(category_id, start_date, end_date);

-- FINANCIAL_GOALS テーブル
CREATE INDEX idx_financial_goals_user_id_deadline ON financial_goals(user_id, deadline);

-- INVESTMENTS テーブル
CREATE INDEX idx_investments_user_id_type ON investments(user_id, type);
CREATE INDEX idx_investments_user_id_purchase_date ON investments(user_id, purchase_date);

-- NOTIFICATIONS テーブル
CREATE INDEX idx_notifications_user_id_created_at ON notifications(user_id, created_at);
CREATE INDEX idx_notifications_user_id_is_read_created_at ON notifications(user_id, is_read, created_at);
```

## 注意点
1. インデックスはクエリのパフォーマンスを向上させますが、書き込み操作のオーバーヘッドも増加させます。
2. 実際の使用パターンに基づいて、インデックスの有効性を定期的に評価し、必要に応じて調整してください。
3. 大規模なデータセットでは、部分インデックスや関数インデックスの使用も検討してください。
4. クエリプランを定期的に分析し、インデックスが適切に使用されていることを確認してください。

## 次のステップ(後日)
1. 各インデックスの影響を測定するためのベンチマークテストの実施
2. 実際のアプリケーション使用パターンに基づくインデックス戦略の微調整
3. 定期的なインデックスメンテナンス計画の策定
