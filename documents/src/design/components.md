# FinFlow: コンポーネント詳細設計仕様書

## 1. ユーザー管理コンポーネント

### 1.1 概要
ユーザーの登録、認証、プロフィール管理を担当するコンポーネント。

### 1.2 主要機能
- ユーザー登録
- ユーザー認証（ログイン/ログアウト）
- パスワードリセット
- プロフィール情報の更新
- アカウント削除

### 1.3 データモデル
```rust
struct User {
    id: Uuid,
    email: String,
    password_hash: String,
    first_name: String,
    last_name: String,
    created_at: DateTime<Utc>,
    updated_at: DateTime<Utc>,
}
```

### 1.4 主要メソッド
- `register_user(email: String, password: String, first_name: String, last_name: String) -> Result<User, Error>`
- `authenticate_user(email: String, password: String) -> Result<AuthToken, Error>`
- `update_profile(user_id: Uuid, profile_data: ProfileUpdateData) -> Result<User, Error>`
- `reset_password(email: String) -> Result<(), Error>`
- `delete_account(user_id: Uuid) -> Result<(), Error>`

### 1.5 外部連携
- 電子メールサービス（登録確認、パスワードリセット用）
- JWT トークン生成サービス（認証用）

## 2. 家計簿管理コンポーネント

### 2.1 概要
ユーザーの収支を記録し、分析するためのコンポーネント。

### 2.2 主要機能
- 収支の記録
- カテゴリ管理
- 定期的な取引の自動記録
- 収支レポートの生成

### 2.3 データモデル
```rust
struct Transaction {
    id: Uuid,
    user_id: Uuid,
    amount: Decimal,
    category_id: Uuid,
    description: Option<String>,
    date: NaiveDate,
    created_at: DateTime<Utc>,
    updated_at: DateTime<Utc>,
}

struct Category {
    id: Uuid,
    name: String,
    type: CategoryType,
    user_id: Option<Uuid>,  // NULLはシステムカテゴリを示す
}

enum CategoryType {
    Income,
    Expense,
}
```

### 2.4 主要メソッド
- `record_transaction(user_id: Uuid, transaction_data: TransactionData) -> Result<Transaction, Error>`
- `get_transactions(user_id: Uuid, start_date: NaiveDate, end_date: NaiveDate) -> Result<Vec<Transaction>, Error>`
- `create_category(user_id: Uuid, name: String, category_type: CategoryType) -> Result<Category, Error>`
- `generate_monthly_report(user_id: Uuid, month: u32, year: u32) -> Result<MonthlyReport, Error>`

### 2.5 外部連携
- 予算管理コンポーネント（予算との比較用）
- 通知コンポーネント（予算超過警告など）

## 3. 予算管理コンポーネント

### 3.1 概要
ユーザーの予算設定と追跡を管理するコンポーネント。

### 3.2 主要機能
- 予算の設定
- 予算の追跡
- 予算超過アラート

### 3.3 データモデル
```rust
struct Budget {
    id: Uuid,
    user_id: Uuid,
    category_id: Uuid,
    amount: Decimal,
    start_date: NaiveDate,
    end_date: NaiveDate,
    created_at: DateTime<Utc>,
    updated_at: DateTime<Utc>,
}
```

### 3.4 主要メソッド
- `set_budget(user_id: Uuid, budget_data: BudgetData) -> Result<Budget, Error>`
- `get_budget_status(user_id: Uuid, category_id: Uuid, date: NaiveDate) -> Result<BudgetStatus, Error>`
- `check_budget_alerts(user_id: Uuid) -> Result<Vec<BudgetAlert>, Error>`

### 3.5 外部連携
- 家計簿管理コンポーネント（実際の支出データ取得用）
- 通知コンポーネント（予算アラート送信用）

## 4. 投資管理コンポーネント

### 4.1 概要
ユーザーの投資ポートフォリオを管理するコンポーネント。

### 4.2 主要機能
- 投資の記録
- ポートフォリオの追跡
- 投資パフォーマンスの分析

### 4.3 データモデル
```rust
struct Investment {
    id: Uuid,
    user_id: Uuid,
    name: String,
    type: InvestmentType,
    amount: Decimal,
    purchase_date: NaiveDate,
    current_value: Decimal,
    created_at: DateTime<Utc>,
    updated_at: DateTime<Utc>,
}

enum InvestmentType {
    Stock,
    Bond,
    MutualFund,
    RealEstate,
    Cryptocurrency,
}
```

### 4.4 主要メソッド
- `record_investment(user_id: Uuid, investment_data: InvestmentData) -> Result<Investment, Error>`
- `update_investment_value(investment_id: Uuid, new_value: Decimal) -> Result<Investment, Error>`
- `get_portfolio_summary(user_id: Uuid) -> Result<PortfolioSummary, Error>`
- `calculate_roi(user_id: Uuid, start_date: NaiveDate, end_date: NaiveDate) -> Result<Decimal, Error>`

### 4.5 外部連携
- 外部金融APIサービス（株価や為替レートの取得用）
- レポート生成コンポーネント（投資レポート作成用）

## 5. 目標設定コンポーネント

### 5.1 概要
ユーザーの財務目標を設定し、進捗を追跡するコンポーネント。

### 5.2 主要機能
- 財務目標の設定
- 目標進捗の追跡
- 目標達成予測

### 5.3 データモデル
```rust
struct FinancialGoal {
    id: Uuid,
    user_id: Uuid,
    name: String,
    target_amount: Decimal,
    current_amount: Decimal,
    deadline: NaiveDate,
    created_at: DateTime<Utc>,
    updated_at: DateTime<Utc>,
}
```

### 5.4 主要メソッド
- `set_financial_goal(user_id: Uuid, goal_data: GoalData) -> Result<FinancialGoal, Error>`
- `update_goal_progress(goal_id: Uuid, new_amount: Decimal) -> Result<FinancialGoal, Error>`
- `get_goal_status(goal_id: Uuid) -> Result<GoalStatus, Error>`
- `predict_goal_achievement(goal_id: Uuid) -> Result<GoalPrediction, Error>`

### 5.5 外部連携
- 家計簿管理コンポーネント（貯蓄状況の取得用）
- 通知コンポーネント（目標達成通知用）

## 6. レポート生成コンポーネント

### 6.1 概要
ユーザーの財務状況に関する各種レポートを生成するコンポーネント。

### 6.2 主要機能
- 月次/年次財務レポートの生成
- 予算vs実績レポートの生成
- 投資パフォーマンスレポートの生成
- カスタムレポートの生成

### 6.3 データモデル
```rust
struct Report {
    id: Uuid,
    user_id: Uuid,
    type: ReportType,
    content: String,  // JSON形式でレポート内容を保存
    generated_at: DateTime<Utc>,
}

enum ReportType {
    Monthly,
    Annual,
    BudgetComparison,
    InvestmentPerformance,
    Custom,
}
```

### 6.4 主要メソッド
- `generate_monthly_report(user_id: Uuid, month: u32, year: u32) -> Result<Report, Error>`
- `generate_annual_report(user_id: Uuid, year: u32) -> Result<Report, Error>`
- `generate_budget_comparison(user_id: Uuid, start_date: NaiveDate, end_date: NaiveDate) -> Result<Report, Error>`
- `generate_investment_report(user_id: Uuid, start_date: NaiveDate, end_date: NaiveDate) -> Result<Report, Error>`

### 6.5 外部連携
- 家計簿管理コンポーネント（取引データ取得用）
- 予算管理コンポーネント（予算データ取得用）
- 投資管理コンポーネント（投資データ取得用）
- PDFエクスポートサービス（レポートの PDF 変換用）

## 7. 通知コンポーネント

### 7.1 概要
ユーザーに各種通知を送信するコンポーネント。

### 7.2 主要機能
- システム通知の管理
- 予算アラートの送信
- 目標達成通知の送信
- カスタム通知の作成と送信

### 7.3 データモデル
```rust
struct Notification {
    id: Uuid,
    user_id: Uuid,
    type: NotificationType,
    message: String,
    is_read: bool,
    created_at: DateTime<Utc>,
}

enum NotificationType {
    System,
    BudgetAlert,
    GoalAchievement,
    Custom,
}
```

### 7.4 主要メソッド
- `create_notification(user_id: Uuid, notification_data: NotificationData) -> Result<Notification, Error>`
- `mark_notification_as_read(notification_id: Uuid) -> Result<(), Error>`
- `get_unread_notifications(user_id: Uuid) -> Result<Vec<Notification>, Error>`
- `send_push_notification(user_id: Uuid, message: String) -> Result<(), Error>`

### 7.5 外部連携
- プッシュ通知サービス（モバイルデバイスへの通知送信用）
- 電子メールサービス（メール通知送信用）
- 各種コンポーネント（通知トリガー用）
