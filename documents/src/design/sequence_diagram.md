## シーケンス図

```mermaid
sequenceDiagram
    actor User
    participant UM as ユーザー管理
    participant FM as 家計簿管理
    participant BM as 予算管理
    participant RM as レポート生成
    participant NM as 通知管理

    User->>UM: ログイン要求
    UM->>User: 認証トークン返却

    User->>FM: 新規取引記録要求
    FM->>UM: ユーザー認証確認
    UM->>FM: 認証確認
    FM->>FM: 取引データ検証
    FM->>FM: 取引記録保存
    FM->>BM: 予算チェック要求

    BM->>BM: 関連予算取得
    BM->>BM: 予算状況計算
    alt 予算超過
        BM->>NM: 予算超過通知作成要求
        NM->>NM: 通知作成
        NM->>User: プッシュ通知送信
    end

    BM->>FM: 予算状況返却
    FM->>RM: 月次レポート更新要求
    RM->>RM: レポートデータ更新

    FM->>User: 取引記録完了応答

    User->>RM: 最新レポート要求
    RM->>UM: ユーザー認証確認
    UM->>RM: 認証確認
    RM->>RM: レポート生成
    RM->>User: 更新されたレポート返却
