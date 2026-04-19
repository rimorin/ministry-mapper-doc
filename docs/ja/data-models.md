# データモデルとスキーマ

## 概要

Ministry Mapper は PocketBase 経由の SQLite を使用し、マルチテナント会衆管理のために設計された階層型データモデルを採用しています。このドキュメントはすべてのデータモデル、関係、ビジネスルールの包括的な説明を提供します。

## エンティティ関係図

```
Congregation（ルートエンティティ - マルチテナント分離）
    │
    ├─── Territory（1:M）
    │      │
    │      └─── Map（1:M）
    │             │
    │             ├─── Address（1:M）
    │             │      ├─── addresses_log（1:M）
    │             │      └─── address_options（1:M）
    │             ├─── Assignment（1:M）
    │             └─── Message（1:M）
    │
    ├─── Option（1:M）
    │      └─── address_options（AddressとのM:Mブリッジ）
    │
    ├─── User（1:M、Role 経由）
    │      │
    │      ├─── Role（1:M）
    │      └─── Assignment（1:M）
    │
    ├─── Message（1:M）
    │
    └─── aggregates（Territory と Map から 1:M）
```

## コアコレクション

### 1. Congregations（会衆）

**目的：** マルチテナント分離と組織設定の中核エンティティ

**タイプ：** ベースコレクション

**フィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `id` | text(15) | はい | 自動生成一意 ID |
| `code` | text | はい | 組織コード |
| `name` | text | はい | 組織名 |
| `expiry_hours` | number | はい | 割り当てリンクの期間（時間） |
| `max_tries` | number | はい | 完了とマークされるまでの最大「不在」試行回数 |
| `origin` | select | はい | 国/地域（sg、my） |
| `timezone` | select | はい | 日付計算のタイムゾーン |
| `created` | date | 自動 | 作成タイムスタンプ |
| `updated` | date | 自動 | 最終更新タイムスタンプ |

**アクセスルール：**
- **リスト：** 会衆に役割を持つユーザー
- **表示：** 役割を持つユーザー OR リンクベースのアクセス
- **更新：** 管理者のみ
- **削除：** 管理者のみ

**ビジネスルール：**
- すべてのマップのデフォルト割り当て期間を制御
- `max_tries` は「不在」の住所が完了とカウントされるタイミングを決定
- タイムゾーンは日付ベースの計算とレポートに影響

**例：**
```json
{
  "id": "abc123",
  "code": "CONG001",
  "name": "北部会衆",
  "expiry_hours": 24,
  "max_tries": 3,
  "origin": "sg",
  "timezone": "Asia/Singapore"
}
```

---

### 2. Territories（テリトリー）

**目的：** フィールド奉仕を整理するための会衆内の地理的区分

**タイプ：** ベースコレクション

**フィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `id` | text(15) | はい | 自動生成一意 ID |
| `congregation` | relation | はい | Congregations への FK（カスケード削除） |
| `code` | text | はい | テリトリー識別子（自動：「0」） |
| `description` | text | いいえ | 人間が読める説明 |
| `progress` | number | 自動 | 完了率（0-100） |
| `created` | date | 自動 | 作成タイムスタンプ |
| `updated` | date | 自動 | 最終更新タイムスタンプ |

**インデックス：**
- `(congregation, code)` - 会衆による高速検索
- `(congregation)` - 会衆フィルター

**関係：**
- Maps と 1:M
- Addresses と 1:M（パフォーマンスのために非正規化）

**アクセスルール：**
- **リスト：** 会衆に役割を持つユーザー
- **表示：** 役割を持つユーザー OR リンクアクセス
- **作成/更新/削除：** コンダクターまたは管理者

**進捗計算：**
テリトリー内のすべてのマップの集計から自動計算

**例：**
```json
{
  "id": "terr_abc",
  "congregation": "cong_123",
  "code": "1A",
  "description": "ダウンタウン地区",
  "progress": 65.5
}
```

---

### 3. Maps（マップ）

**目的：** 住所を含む特定の場所または建物を表す

**タイプ：** ベースコレクション

**フィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `id` | text(15) | はい | 自動生成一意 ID |
| `territory` | relation | はい | Territories への FK（カスケード削除） |
| `congregation` | relation | はい | Congregations への FK（カスケード削除） |
| `sequence` | number | 自動 | テリトリー内の順序（自動インクリメント） |
| `code` | text | はい | マップ識別子/番号（自動：「0」） |
| `description` | text | いいえ | 場所の説明 |
| `type` | select | はい | `single` または `multi`（フロアタイプ） |
| `coordinates` | JSON | いいえ | 地理的位置 `{lat, lng}` |
| `aggregates` | JSON | 自動 | キャッシュされた統計（最大 2MB） |
| `progress` | number | 自動 | 完了率 |
| `created` | date | 自動 | 作成タイムスタンプ |
| `updated` | date | 自動 | 最終更新タイムスタンプ |

**インデックス：**
- `(territory, code)` - プライマリ検索
- `(territory, sequence)` - 順序付け
- `(territory)` - テリトリーフィルター

**カスケード削除：**
- Addresses
- Assignments
- Messages

**集計構造：**
```json
{
  "done": 45,
  "not_done": 30,
  "dnc": 5,
  "invalid": 2,
  "not_home_max_tries": 8
}
```

**マップタイプ：**
- **single**：個人住所（一戸建て）
- **multi**：公共住所（多層建物）

**アクセスルール：**
- **リスト：** 役割を持つユーザー OR リンクアクセス
- **表示：** 役割を持つユーザー OR リンクアクセス
- **作成/更新：** コンダクターまたは管理者
- **削除：** 管理者のみ

**例：**
```json
{
  "id": "map_xyz",
  "territory": "terr_abc",
  "congregation": "cong_123",
  "sequence": 5,
  "code": "12",
  "description": "メインストリートアパート",
  "type": "multi",
  "coordinates": {"lat": 1.3521, "lng": 103.8198},
  "aggregates": {
    "done": 20,
    "not_done": 10,
    "dnc": 2,
    "invalid": 1,
    "not_home_max_tries": 3
  },
  "progress": 66.7
}
```

---

### 4. Addresses（住所）

**目的：** マップ内の個別ユニットまたは世帯

**タイプ：** ベースコレクション

**フィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `id` | text(15) | はい | 自動生成一意 ID |
| `map` | relation | はい | Maps への FK（カスケード削除） |
| `congregation` | relation | はい | Congregations への FK（非正規化） |
| `territory` | relation | はい | Territories への FK（非正規化） |
| `floor` | number | はい | フロア番号（0ベース） |
| `code` | text | はい | ユニット/ドアコード（例：「101」、「A」） |
| `sequence` | number | はい | マップ内の順序 |
| `status` | select | はい | 住所の訪問ステータス |
| `not_home_tries` | number | 自動 | 「不在」試行回数カウンター |
| `type` | relation | いいえ | Options への M:M（JSON 配列） |
| `notes` | text | いいえ | フィールドワーカーのメモ |
| `last_notes_updated` | date | 自動 | メモが最後に変更された日時（フック経由） |
| `last_notes_updated_by` | text | 自動 | 最終更新者のユーザー名（フック経由） |
| `dnc_time` | date | いいえ | 「訪問禁止」とマークされた日時 |
| `coordinates` | JSON | いいえ | 詳細な GPS `{lat, lng}` |
| `updated_by` | text | いいえ | 更新者を追跡 |
| `created` | date | 自動 | 作成タイムスタンプ |
| `updated` | date | 自動 | 最終更新タイムスタンプ |

**ステータス値：**
- `not_done` - 未訪問（初期状態）
- `done` - 正常完了
- `not_home` - 誰も在宅でない（再試行可能）
- `do_not_call` - 奉仕から除外
- `invalid` - 住所が存在しない

**インデックス（計7つ）：**
- `(map)` - マップフィルター
- `(map, status)` - ステータス分布クエリ
- `(map, code)` - コード検索
- `(map, floor)` - フロア整理
- `(type, congregation)` - オプション分布
- `(congregation, last_notes_updated_by)` - ユーザー別の最近のメモ
- `(territory, status)` - テリトリー全体のステータス

**自動フック：**
- 更新は `last_notes_updated` と `last_notes_updated_by` フィールドをトリガー
- マップの集計再計算をトリガー
- テリトリーの進捗を更新

**アクセスルール：**
- **リスト：** 役割を持つユーザー OR リンクアクセス
- **表示：** 役割を持つユーザー OR リンクアクセス
- **作成：** コンダクターまたは管理者
- **更新：** 役割を持つユーザー OR リンクアクセス（伝道者が更新可能）
- **削除：** 管理者のみ

**例：**
```json
{
  "id": "addr_123",
  "map": "map_xyz",
  "congregation": "cong_123",
  "territory": "terr_abc",
  "floor": 2,
  "code": "201",
  "sequence": 5,
  "status": "not_home",
  "not_home_tries": 2,
  "type": ["opt_residential"],
  "notes": "18時以降に試みてください",
  "last_notes_updated": "2025-12-19T14:30:00Z",
  "last_notes_updated_by": "john.doe",
  "coordinates": {"lat": 1.3521, "lng": 103.8198}
}
```

---

### 5. Users（ユーザー）

**目的：** 認証付きシステムユーザーアカウント

**タイプ：** Auth コレクション（PocketBase ユーザー認証タイプ）

**システムフィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `id` | text(15) | はい | 自動生成一意 ID |
| `email` | email | はい | ログイン識別子（一意） |
| `emailVisibility` | bool | はい | メールが公開かどうか |
| `verified` | bool | 自動 | メール確認ステータス |
| `password` | password | はい | Bcrypt ハッシュ（コスト：10、最小6文字） |
| `tokenKey` | text | 非表示 | トークン生成キー（30〜60文字） |

**カスタムフィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `name` | text | はい | 表示名（2〜50文字） |
| `disabled` | bool | いいえ | アカウント無効フラグ |
| `last_login` | date | 自動 | 最終認証タイムスタンプ（フック経由） |

**名前のバリデーションパターン：**
`^[A-Za-z][\w\s\.\-']*$`（文字で始まり、単語文字、スペース、ドット、ハイフン、アポストロフィを許可）

**自動生成パターン：**
`user[0-9]{5}[A-Za-z]`（例：user12345A）

**インデックス：**
- `email`（一意）- 高速認証検索
- `tokenKey`（一意）- トークン検証

**認証設定：**
- **パスワード認証：** 有効（最小6文字）
- **OAuth2：** Google PKCE フロー
  - OAuth プロバイダーから `name` をマッピング
  - 初回ログイン時に自動ユーザー作成
- **MFA：** 有効（期間：1800秒）- OAuth2 では無効
- **OTP：** 有効（期間：180秒、長さ：4桁）
- **メール確認：** 期間 604800秒（7日）
- **パスワードリセット：** 期間 1800秒（30分）
- **認証アラート：** 有効（新しい場所からのログイン時に通知）

**アクセスルール：**
- **リスト：** 管理者のみ
- **表示：** 管理者または本人
- **作成：** 公開（OAuth またはユーザー登録）
- **更新：** 本人のみ
- **削除：** 管理者のみ

**ファイルトークン：** 120秒の期間

**関係：**
- Roles と 1:M
- Assignments と 1:M

**例：**
```json
{
  "id": "user_abc",
  "email": "john.doe@example.com",
  "name": "John Doe",
  "verified": true,
  "disabled": false,
  "last_login": "2025-12-19T14:30:00Z"
}
```

---

### 6. Roles（役割）

**目的：** ユーザーを会衆にリンクする権限割り当て

**タイプ：** ベースコレクション

**フィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `id` | text(15) | はい | 自動生成一意 ID |
| `user` | relation | はい | Users への FK（カスケード削除） |
| `congregation` | relation | はい | Congregations への FK（カスケード削除） |
| `role` | select | はい | 権限レベル |
| `created` | date | 自動 | 作成タイムスタンプ |
| `updated` | date | 自動 | 最終更新タイムスタンプ |

**役割の値：**
- `read_only` - 閲覧のみアクセス
- `conductor` - 割り当てとマップの管理が可能
- `administrator` - フルアクセス

**インデックス：**
- `(user, congregation)` - 複合キー（会衆ごとに一役割）
- `(user)` - ユーザーの役割
- `(congregation, role)` - 役割の分布
- `(user, congregation, role)` - フル複合検索

**アクセスルール：**
- **作成/更新/削除：** 管理者のみ
- **リスト：** 自分の役割
- **表示：** 自分の役割

**関係：**
- Users と Congregations の間の M:N

**ビジネスルール：**
- 会衆ごとにユーザー1人につき1役割
- 役割がすべてのアクションの権限を決定

**例：**
```json
{
  "id": "role_xyz",
  "user": "user_abc",
  "congregation": "cong_123",
  "role": "conductor"
}
```

---

### 7. Assignments（割り当て）

**目的：** 時間制限付きマップアクセスのための伝道者アクセストークン

**タイプ：** ベースコレクション

**フィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `id` | text(20) | はい | **匿名アクセストークンとして機能** |
| `congregation` | relation | はい | Congregations への FK（カスケード削除） |
| `map` | relation | はい | Maps への FK（カスケード削除） |
| `user` | relation | いいえ | 伝道者ユーザー（オプション） |
| `type` | select | はい | 割り当て分類 |
| `expiry_date` | date | はい | 自動期限切れタイムスタンプ |
| `publisher` | text | いいえ | 伝道者名/識別子 |
| `created` | date | 自動 | 作成タイムスタンプ |
| `updated` | date | 自動 | 最終更新タイムスタンプ |

**割り当てタイプ：**
- `normal` - 通常のテリトリー作業
- `personal` - 個人奉仕テリトリー

**インデックス：**
- `(map)` - マップごとのアクティブな割り当て
- `(expiry_date)` - バックグラウンドクリーンアップジョブ用
- `(user, created)` - ユーザーの割り当て履歴
- `(map, expiry_date)` - マップごとの期限確認

**アクセスルール：**
- **作成/削除：** コンダクターまたは管理者
- **表示：** 役割を持つユーザー OR この ID に一致する link_id + 期限切れでない
- **リスト：** 役割を持つユーザー

**ワークフロー：**
1. コンダクターが `/territory/link` エンドポイント経由で作成
2. 伝道者が20文字の `link_id`（この割り当ての ID）を受け取る
3. 伝道者が HTTP ヘッダー `@request.headers.link_id` で `link_id` を渡す
4. PocketBase が検証：link_id が assignment.id に一致し、かつ expiry_date > 現在時刻
5. バックグラウンドジョブが5分ごとに期限切れ割り当てを削除

**例：**
```json
{
  "id": "asgn_1234567890abcdef",
  "congregation": "cong_123",
  "map": "map_xyz",
  "user": "user_abc",
  "type": "normal",
  "expiry_date": "2025-12-20T14:30:00Z",
  "publisher": "John Doe"
}
```

---

### 8. Options（オプション）

**目的：** 住所タイプの分類（会衆ごとにカスタマイズ可能）

**タイプ：** ベースコレクション

**フィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `id` | text(15) | はい | 自動生成一意 ID |
| `congregation` | relation | はい | Congregations への FK（カスケード削除） |
| `code` | text | はい | オプション識別子（例：「RES」、「BUS」） |
| `description` | text | はい | 人間が読めるラベル |
| `is_countable` | bool | はい | 進捗計算に含める |
| `is_default` | bool | はい | 新しい住所のデフォルトオプション |
| `sequence` | number | はい | 表示/ソート順序 |
| `created` | date | 自動 | 作成タイムスタンプ |
| `updated` | date | 自動 | 最終更新タイムスタンプ |

**インデックス：**
- `(congregation, sequence)` - 会衆内での順序付け
- `(congregation, is_default)` - デフォルトオプションの検索

**制約：**
- 会衆ごとに `is_default = true` は正確に1つ（アプリケーション層で強制）
- コードは会衆ごとに一意
- シーケンスは会衆ごとに一意

**進捗への影響：**
- カウント可能なオプション（`is_countable=true`）は進捗 % 計算に含まれる
- カウント不可能なオプションは別途追跡されるが進捗から除外
- オプションが削除されると、住所には自動的にデフォルトオプションが追加される

**アクセスルール：**
- **リスト/表示：** 役割を持つユーザー OR リンクアクセス
- **作成/更新/削除：** `/options/update` エンドポイントを使用（コンダクターまたは管理者）

**関係：**
- Addresses と M:M（address.type フィールドに JSON 配列として保存）

**例：**
```json
{
  "id": "opt_res",
  "congregation": "cong_123",
  "code": "RES",
  "description": "住宅",
  "is_countable": true,
  "is_default": true,
  "sequence": 1
}
```

---

### 9. Messages（メッセージ）

**目的：** ユーザー間のコミュニケーションと通知

**タイプ：** ベースコレクション

**フィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `id` | text(15) | はい | 自動生成一意 ID |
| `map` | relation | はい | Maps への FK（カスケード削除） |
| `congregation` | relation | はい | Congregations への FK（カスケード削除） |
| `message` | text | はい | メッセージ内容 |
| `created_by` | text | はい | 作成者のユーザー名 |
| `read` | bool | はい | 管理者が読んだかどうか |
| `pinned` | bool | はい | 管理者のピン留め指示フラグ |
| `type` | select | はい | 意図した受信者タイプ |
| `created` | date | 自動 | 作成タイムスタンプ |
| `updated` | date | 自動 | 最終更新タイムスタンプ |

**メッセージタイプ：**
- `publisher` - 伝道者から管理者へ
- `conductor` - コンダクターから
- `administrator` - 管理者の指示（ピン留め可能）

**インデックス：**
- `(map, pinned, created)` - 最近のピン留めメッセージ
- `(map, type, pinned)` - 受信者タイプとピン留めステータスでフィルター
- `(map, type, read)` - タイプごとの未読カウント

**アクセスルール：**
- **作成：** 役割を持つユーザー OR リンクアクセス
- **更新：** 管理者のみ
- **リスト：** 役割を持つユーザー OR リンクアクセス

**バックグラウンド処理：**
- 30分ごと：未読メッセージがメール経由で管理者に送信
- 30分ごと：ピン留めされた管理者メッセージがメール経由で伝道者に送信
- 1時間ごと：メモの更新が管理者に送信

**例：**
```json
{
  "id": "msg_abc",
  "map": "map_xyz",
  "congregation": "cong_123",
  "message": "205号室を再訪してください",
  "created_by": "john.doe",
  "read": false,
  "pinned": false,
  "type": "publisher"
}
```

---

### 10. Addresses Log（住所ログ）

**目的：** 住所ステータス変更の監査証跡 — 住所ステータスが更新されるたびにここにレコードが書き込まれます

**タイプ：** ベースコレクション

**フィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `id` | text(15) | はい | 自動生成一意 ID |
| `address` | relation | はい | Addresses への FK（カスケード削除） |
| `old_status` | text | はい | 変更前の住所ステータス値 |
| `new_status` | text | はい | 変更後の住所ステータス値 |
| `changed_by` | relation | いいえ | Users への FK — 変更を行ったユーザー |
| `created` | date | 自動 | 変更のタイムスタンプ |

**関係：**
- Addresses と M:1（各ログエントリは1つの住所に属する）
- Users と M:1（`changed_by` 経由）

**アクセスルール：**
- **リスト/表示：** コンダクターまたは管理者
- **作成：** システムフック（住所ステータス変更時に自動）
- **更新/削除：** 管理者のみ

---

### 11. Aggregates（集計）

**目的：** テリトリーとマップのキャッシュされた進捗スナップショット。バックグラウンドジョブにより10分ごとに再計算されます

**タイプ：** ベースコレクション

**フィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `id` | text(15) | はい | 自動生成一意 ID |
| `notDone` | number | はい | まだ訪問されていない住所の数 |
| `notHome` | number | はい | 「不在」住所の数 |
| `progress` | number | はい | 完了率（0〜100） |
| `territory` | relation | いいえ | Territories への FK — テリトリーレベルの集計に設定 |
| `map` | relation | いいえ | Maps への FK — マップレベルの集計に設定 |

**関係：**
- Territories と M:1（テリトリーレベルのスナップショット）
- Maps と M:1（マップレベルのスナップショット）

**ビジネスルール：**
- 各レコードに `territory` または `map` のいずれか一方が設定されます
- バックグラウンドジョブにより10分ごとに再計算
- 値は読み取り専用のスナップショット；ライブ進捗はオンデマンドで計算

**アクセスルール：**
- **リスト/表示：** 役割を持つユーザー OR リンクアクセス
- **作成/更新：** システムバックグラウンドジョブのみ
- **削除：** 管理者のみ

---

### 12. Address Options（住所オプション）

**目的：** 個々の住所に適用されている住所レベルのオプション（`options` コレクションから）を追跡 — Address ↔ Option の多対多関係のジャンクションテーブル

**タイプ：** ベースコレクション

**フィールド：**

| フィールド | タイプ | 必須 | 説明 |
|-------|------|----------|-------------|
| `id` | text(15) | はい | 自動生成一意 ID |
| `address` | relation | はい | Addresses への FK（カスケード削除） |
| `option` | relation | はい | Options への FK（カスケード削除） |
| `congregation` | relation | はい | Congregations への FK（高速クエリのため非正規化） |

**インデックス：**
- `(address, option)` — 一意ペアリング（住所ごとに1オプション）
- `(congregation)` — 会衆全体のオプション使用状況

**関係：**
- Addresses と M:1
- Options と M:1
- Congregations と M:1

**アクセスルール：**
- **リスト/表示：** 役割を持つユーザー OR リンクアクセス
- **作成/削除：** コンダクターまたは管理者

---

## アナリティクスコレクション

アナリティクスコレクションはレポートとインサイトのための履歴データを保存します。これらのコレクションは内部的に管理され、正確なフィールドは時間とともに変化する可能性があります；個別フィールドのテーブルではなく目的で説明します。

### analytics_territories

**目的：** 時系列のテリトリーレベルメトリクス（進捗率、住所数など）の定期スナップショットを保存します。履歴トレンドチャートとテリトリー完了レポートの生成に使用されます。

### analytics_maps

**目的：** 時系列のマップレベルメトリクスの定期スナップショットを保存します。個別のマップ完了率とアクティビティパターンのきめ細かいレポートを実現します。

### analytics_daily_status

**目的：** 会衆全体の住所ステータス（完了、未完了、不在、訪問不可、無効）の日次集計カウント。日ごとおよび週ごとのトレンドダッシュボードを提供します。

### analytics_not_home

**目的：** 繰り返し「不在」とマークされた住所を追跡します。司会者のフォローアップ対象の特定や、継続的に連絡が取れない住所の識別に使用されます。

### analytics_user_audit

**目的：** ログイン、住所更新、割り当て操作などのユーザーアクションを記録し、説明責任と監査の目的に使用されます。管理者がアクティビティ履歴を確認し、異常を調査できます。

> **注意：** アナリティクスコレクションはバックグラウンドジョブによって書き込まれ、レポートレイヤーによって読み取られます。アプリケーションコードからの直接書き込みは避けてください。

---

## データベース技術

**エンジン：** PocketBase 経由の SQLite（modernc.org/sqlite v1.40.2+）

**機能：**
- 純粋な Go 実装（CGo フリー）- クロスプラットフォームコンパイルを可能にする
- ccgo/v4 を使用して Go にトランスパイルされた C SQLite ベース
- 定期的なセキュリティアップデートで積極的にメンテナンス（SQLite 3.50+ に準拠）

**使用されている SQL 機能：**
- 柔軟なデータのための JSON フィールド（座標、集計）
- クエリ最適化のための複合インデックス
- ACID コンプライアンスのためのトランザクション
- 複雑な集計のための CTE（Common Table Expressions）

**利点：**
- 自己完結型ファイルベースのデータベース
- 別途データベースサービス不要
- セルフホストデプロイに最適
- CGO 依存なし - 静的バイナリビルドが可能
- セキュリティパッチによる実証された安定性

---

## 主要な関係

| 関係 | カーディナリティ | カスケード動作 |
|--------------|-------------|------------------|
| Congregation → Territory | 1:M | 削除カスケード |
| Territory → Map | 1:M | 削除カスケード |
| Map → Address | 1:M | 削除カスケード |
| Map → Assignment | 1:M | 削除カスケード |
| Map → Message | 1:M | 削除カスケード |
| Address → Options | M:M | JSON 配列 |
| User → Role | M:M | roles テーブル経由 |
| User → Assignment | 1:M | 割り当てを保持 |
| Congregation → Option | 1:M | 削除カスケード |
| Congregation → Role | M:M | roles テーブル経由 |

---

## ビジネスルール

### 進捗計算

**計算式：**
```
completed = (done + not_home_max_tries) / total_countable * 100
```

**カウント可能な住所：**
- `is_countable=true` オプションを持つ住所のみ
- DNC と無効な住所を除外

**完了基準：**
- ステータス = `done`、または
- ステータス = `not_home` かつ `not_home_tries >= congregation.max_tries`

### 住所ライフサイクル

```
not_done → not_home（再試行）→ done
         → do_not_call（除外）
         → invalid（除外）
```

### 自動更新トリガー

1. **住所更新：**
   - メモが変更された場合 `last_notes_updated` を更新
   - 現在のユーザーに `last_notes_updated_by` を設定
   - 集計再計算をトリガー
   - マップの進捗を更新
   - テリトリーの進捗を更新

2. **マップ操作：**
   - 住所が変更されたときに集計を再計算
   - テリトリーの進捗を更新

3. **割り当て作成：**
   - 会衆の設定に基づいて期限切れを設定
   - アクセストークンとして一意の20文字 ID を生成

---

## マイグレーションとスキーマ更新

**スキーマファイル：** `migrations/1764846700_collections_snapshot.go`

**サイズ：** 55,459行（PocketBase が生成）

**マイグレーションプロセス：**
1. PocketBase は起動時に自動的にマイグレーションを適用
2. マイグレーションは `_migrations` システムコレクションで追跡
3. スキーマ変更には新しいマイグレーションファイルが必要

---

## 次のステップ

- [アーキテクチャ](architecture.md) - システムアーキテクチャ
- [バックエンドセットアップ](backend-setup.md) - データベースの設定
