# FinFlow オブジェクト一覧

## 1. ユーザー管理サービス
- User
  - id: UUID
  - username: String
  - email: String
  - password: String (ハッシュ化)
  - firstName: String
  - lastName: String
  - dateOfBirth: Date
  - phoneNumber: String
  - address: Address
  - createdAt: DateTime
  - updatedAt: DateTime

- Address
  - street: String
  - city: String
  - state: String
  - country: String
  - postalCode: String

- UserPreferences
  - userId: UUID
  - language: String
  - currency: String
  - notificationSettings: NotificationSettings

- NotificationSettings
  - emailNotifications: Boolean
  - pushNotifications: Boolean
  - smsNotifications: Boolean

## 2. 取引管理サービス
- Transaction
  - id: UUID
  - userId: UUID
  - amount: BigDecimal
  - type: TransactionType (INCOME, EXPENSE)
  - category: String
  - description: String
  - date: Date
  - accountId: UUID
  - createdAt: DateTime
  - updatedAt: DateTime

- RecurringTransaction
  - id: UUID
  - userId: UUID
  - amount: BigDecimal
  - type: TransactionType
  - category: String
  - description: String
  - frequency: Frequency (DAILY, WEEKLY, MONTHLY, YEARLY)
  - startDate: Date
  - endDate: Date (optional)
  - accountId: UUID

## 3. 予算管理サービス
- Budget
  - id: UUID
  - userId: UUID
  - name: String
  - amount: BigDecimal
  - period: Period (WEEKLY, MONTHLY, YEARLY)
  - category: String
  - startDate: Date
  - endDate: Date
  - createdAt: DateTime
  - updatedAt: DateTime

- BudgetCategory
  - id: UUID
  - name: String
  - parentCategoryId: UUID (optional)

## 4. 投資管理サービス
- Investment
  - id: UUID
  - userId: UUID
  - type: InvestmentType (STOCK, BOND, MUTUAL_FUND, ETF, REAL_ESTATE)
  - symbol: String
  - name: String
  - quantity: BigDecimal
  - purchasePrice: BigDecimal
  - purchaseDate: Date
  - currentPrice: BigDecimal
  - lastUpdated: DateTime

- Portfolio
  - id: UUID
  - userId: UUID
  - name: String
  - description: String
  - createdAt: DateTime
  - updatedAt: DateTime

- PortfolioInvestment
  - portfolioId: UUID
  - investmentId: UUID
  - allocation: BigDecimal

## 5. 目標管理サービス
- FinancialGoal
  - id: UUID
  - userId: UUID
  - name: String
  - targetAmount: BigDecimal
  - currentAmount: BigDecimal
  - deadline: Date
  - category: GoalCategory
  - status: GoalStatus (IN_PROGRESS, ACHIEVED, FAILED)
  - createdAt: DateTime
  - updatedAt: DateTime

- GoalCategory
  - id: UUID
  - name: String
  - description: String

## 6. 金融機関連携サービス
- FinancialAccount
  - id: UUID
  - userId: UUID
  - institutionId: UUID
  - accountNumber: String (暗号化)
  - accountType: AccountType (CHECKING, SAVINGS, CREDIT_CARD, INVESTMENT)
  - balance: BigDecimal
  - currency: String
  - lastSyncedAt: DateTime

- FinancialInstitution
  - id: UUID
  - name: String
  - type: InstitutionType (BANK, CREDIT_UNION, BROKERAGE)
  - apiCredentials: ApiCredentials

- ApiCredentials
  - apiKey: String (暗号化)
  - apiSecret: String (暗号化)
  - tokenExpirationDate: DateTime

## 7. AI分析サービス
- FinancialInsight
  - id: UUID
  - userId: UUID
  - type: InsightType (SPENDING_PATTERN, SAVING_OPPORTUNITY, INVESTMENT_ADVICE)
  - description: String
  - createdAt: DateTime

- PredictionModel
  - id: UUID
  - userId: UUID
  - type: ModelType (INCOME_PREDICTION, EXPENSE_PREDICTION, INVESTMENT_RETURN_PREDICTION)
  - parameters: Map<String, Object>
  - accuracy: Double
  - lastTrainedAt: DateTime

- AnalysisReport
  - id: UUID
  - userId: UUID
  - type: ReportType (MONTHLY_SUMMARY, ANNUAL_REVIEW, INVESTMENT_PERFORMANCE)
  - content: String (JSON)
  - generatedAt: DateTime
