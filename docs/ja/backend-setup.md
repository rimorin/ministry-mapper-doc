# バックエンドセットアップガイド

!!! warning "セルフホストは推奨しません"
    このガイドは Ministry Mapper をセルフホストしたい上級ユーザー向けです。技術的な複雑さ、メンテナンスの負担、セキュリティの責任のため、セルフホストはお勧めしません。
    
    **ほとんどのユーザーは**：**[ministry-mapper.com](https://ministry-mapper.com)** のホストサービスを使用してください。

!!! info "ユーザードキュメントをお探しですか？"
    Ministry Mapper の使い方を学びたい一般ユーザーの方は、代わりに[はじめかた](getting-started.md)または[ユーザーガイド](user-guide.md)をご覧ください。

## 概要

Ministry Mapper バックエンド（ministry-mapper-be）は Go と PocketBase で構築されています。以下を提供します：

- データストレージと管理（SQLite データベース）
- ユーザー認証と認可
- リアルタイムデータ同期
- テリトリー管理のカスタム API エンドポイント
- 自動タスク用のバックグラウンドジョブ
- SMTP 経由のメール通知

**このガイドは以下を前提としています**：
- サーバー管理の経験
- Docker とコンテナ化の理解
- 環境変数の知識
- セキュリティのベストプラクティスの知識

完全なセルフホスト手順については、[セルフホストガイド](self-hosting.md)を参照してください。

## クイックリファレンス

このページはバックエンド設定のクイックリファレンスを提供します。**完全なセットアップ手順については、[セルフホストガイド](self-hosting.md)を参照してください。**

### 技術スタック

- **Go**：高速で効率的なプログラミング言語
- **PocketBase**：組み込み管理パネル付きの Backend-as-a-Service
- **SQLite**：`pb_data` フォルダの軽量データベース
- **Docker**：Alpine Linux ベースのコンテナ

### 主要機能

- データストレージと管理
- ユーザー認証
- リアルタイムサブスクリプション（Server-Sent Events）
- カスタム API エンドポイント
- スケジュールされたバックグラウンドジョブ
- メール通知（SMTP）
- エラー追跡（Sentry）
- フィーチャーフラグ（LaunchDarkly）

---

!!! note "完全なセットアップ手順"
    以下のセクションはバックエンド設定の技術的なリファレンスを提供します。コンテキストと説明付きのステップバイステップのセットアップ手順については、**[セルフホストガイド](self-hosting.md)** を参照してください。

---

## 環境変数の説明

### サービス統合キー（オプション）

```bash
# MailerSend API キー - MailerSend サービス経由でメールを送信するため
# SMTP を使用する場合は空のままにしてください
MAILERSEND_API_KEY=

# MailerSend 送信者メールアドレス
MAILERSEND_FROM_EMAIL=

# LaunchDarkly SDK キー - バックグラウンドジョブを制御するフィーチャーフラグ用
# 設定されていない場合、ジョブはデフォルトで実行されます
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=

# OpenAI API キー - 月次レポートとメッセージダイジェストの AI による要約用
# オプション：AI セクションが省略されるだけで、レポートとダイジェストはこれなしでも問題なく機能します
OPENAI_API_KEY=
```

### アプリケーション設定

```bash
# メールと管理パネルに表示される名前
PB_APP_NAME=Ministry Mapper

# フロントエンドがホストされている URL
PB_APP_URL=http://localhost:3000
```

### デフォルト管理者アカウント

```bash
# 初回セットアップ時に作成される初期管理者アカウント
# 初回ログイン後すぐにパスワードを変更してください！
PB_ADMIN_EMAIL=testing_account@ministry-mapper.com
PB_ADMIN_PASSWORD=pb123456789
```

### メール設定（SMTP）

```bash
# SMTP サーバーアドレス（例：smtp.gmail.com、smtp.sendgrid.net）
PB_SMTP_HOST=

# SMTP パスワード（Gmail の場合は通常のパスワードではなくアプリパスワードを使用）
PB_SMTP_PASSWORD=

# SMTP ユーザー名（通常はメールアドレス）
PB_SMTP_USERNAME=

# SMTP ポート（TLS は 587、SSL は 465）
PB_SMTP_PORT=587

# 「From」フィールドに表示されるメールアドレス
PB_SMTP_SENDER_ADDRESS=support@ministry-mapper.com

# 「From」フィールドに表示される名前
PB_SMTP_SENDER_NAME=MM Support
```

### セキュリティとアクセス制御

```bash
# CORS で許可されるオリジンのカンマ区切りリスト
# 開発時は *、本番環境では特定のドメインを使用
# 例：https://app.example.com,https://app2.example.com
PB_ALLOW_ORIGINS=*

# 管理 UI を公開アクセスから非表示にする（true/false）
# セキュリティのため本番環境では true に設定
PB_HIDE_CONTROLS=false
```

### 認証設定

```bash
# ワンタイムパスワードログインを有効にする（true/false）
PB_OTP_ENABLED=false

# 多要素認証を有効にする（true/false）
PB_MFA_ENABLED=false
```

### レート制限

```bash
# API の乱用を防ぐためのレート制限を有効にする（true/false）
PB_ENABLE_RATE_LIMITING=false
```

### エラー追跡（.env.sample にない場合は手動で追加）

```bash
# エラー監視と追跡用の Sentry DSN
SENTRY_DSN=

# Sentry の環境名（development、staging、production）
SENTRY_ENV=production

# Git コミットハッシュ - Coolify などのホスティングプラットフォームで自動設定
SOURCE_COMMIT=
```

## フィーチャーフラグ

Ministry Mapper は [LaunchDarkly](https://launchdarkly.com) を使用してバックグラウンドジョブの実行をゲートします。各ジョブは再デプロイなしに独立して有効化・無効化できます。フィーチャーフラグには `LAUNCHDARKLY_SDK_KEY` と `LAUNCHDARKLY_CONTEXT_KEY` の設定が必要です。

| フラグキー | 制御対象ジョブ | 説明 |
|----------|----------------|-------------|
| `enable-assignments-cleanup` | `cleanUpAssignments` | 期限切れのマップ割り当てを削除（5分ごとに実行） |
| `enable-territory-aggregations` | `updateTerritoryAggregates` | テリトリーの進捗統計を再計算（10分ごとに実行） |
| `enable-message-processing` | `processMessages` | 保留中の伝道者メッセージを処理（30分ごとに実行） |
| `enable-instruction-processing` | `processInstructions` | テリトリー割り当て指示を処理（30分ごとに実行） |
| `enable-note-processing` | `processNotes` | 更新された会衆メモを処理（1時間ごとに実行） |
| `enable-monthly-report` | `generateMonthlyReport` | Excel レポートを生成してメール送信（毎月1日に実行） |
| `enable-unprovisioned-user-processing` | `processUnprovisionedUsers` | 役割が割り当てられていないユーザーを警告・無効化（毎日実行） |
| `enable-inactive-user-processing` | `processInactiveUsers` | 非アクティブなアカウントを警告・無効化 — NIST AC-2(3)（毎日実行） |
| `enable-new-addresses-notification` | `processNewAddresses` | アプリで追加された住所のダイジェストメールを送信（毎日実行） |
| `enable-report-ai-summary` | レポート内の AI 要約 | 会衆レポートに OpenAI 生成のサマリーを含める |

!!! note
    LaunchDarkly が設定されていない場合、すべてのジョブはデフォルトで実行されます（enabled: true）。

## インストール手順

### オプション1：Docker デプロイ（推奨）

Docker でデプロイがシンプルかつプラットフォーム間で一貫したものになります。

#### 1. Docker イメージのビルド

```bash
docker build -t ministry-mapper-backend .
```

#### 2. コンテナの実行

```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**重要なメモ：**

- コンテナはポート **8080**（8090 ではない）で実行されます
- `-p 8080:8080` でコンテナのポート 8080 をホストのポート 8080 にマップします
- `-v /path/to/pb_data:/app/pb_data` でデータベースを永続的に保存します（`/path/to/pb_data` を実際のパスに置き換えてください）
- `--env-file .env` で `.env` ファイルから環境変数を読み込みます
- `-d` でコンテナをバックグラウンドで実行します（デタッチモード）

### オプション2：ローカル開発

コンピュータでの開発とテスト用：

#### 1. Go 依存関係のインストール

```bash
./scripts/install.sh
```

このスクリプトは `go get ./...` と `go mod tidy` を実行してすべての依存関係をインストールします。

#### 2. 環境変数のセットアップ

```bash
cp .env.sample .env
# .env を設定で編集
```

#### 3. アプリケーションの実行

```bash
./scripts/start.sh
```

このスクリプトは `.env` から環境変数をエクスポートして `go run main.go serve` を実行します。

バックエンドはデフォルトで **http://localhost:8090** で起動します（PocketBase のデフォルトポート）。

## 管理パネルへのアクセス

バックエンドが実行されたら：

1. `http://your-backend-url/_/` にアクセス（アンダースコアとスラッシュに注意）
2. `PB_ADMIN_EMAIL` と `PB_ADMIN_PASSWORD` の管理者認証情報でログイン
3. PocketBase 管理ダッシュボードが表示されます

### 管理パネルでできること

- **コレクション**：すべてのデータベーステーブルの表示と編集（ユーザー、会衆、テリトリー、マップ、住所など）
- **ログ**：アプリケーションとリクエストのログの表示
- **設定**：認証、メール、バックアップなどの設定
- **ファイル**：アップロードされたファイルとストレージの管理
- **API ルール**：各コレクションの権限設定

**セキュリティメモ**：管理 UI への公開アクセスを非表示にするため、本番環境では `PB_HIDE_CONTROLS=true` を設定してください。

## データベース管理

### データのバックアップ

データは `pb_data` フォルダに保存されています。定期的なバックアップが重要です。

#### 手動バックアップ

```bash
# pb_data フォルダ全体のバックアップを作成
tar -czf ministry-mapper-backup-$(date +%Y%m%d).tar.gz /path/to/pb_data/
```

#### 自動日次バックアップ

自動バックアップ用の cron ジョブを設定：

```bash
# crontab に追加（実行：crontab -e）
0 2 * * * tar -czf /backups/ministry-mapper-backup-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

これは毎日午前2時に実行され、タイムスタンプ付きのバックアップを作成します。

#### バックアップからの復元

```bash
# まずアプリケーションを停止
# その後バックアップを解凍
tar -xzf ministry-mapper-backup-20240101.tar.gz -C /path/to/
```

## セキュリティチェックリスト

本番環境に移行する前に：

- [ ] 初回ログイン後すぐにデフォルトの管理者パスワードを変更
- [ ] `PB_ALLOW_ORIGINS` を `*` ではなく実際のドメインに設定
- [ ] HTTPS を有効化（本番環境では HTTP を使用しない）
- [ ] 不正アクセスから管理 UI を非表示にするため `PB_HIDE_CONTROLS=true` を設定
- [ ] `PB_ENABLE_RATE_LIMITING=true` でレート制限の有効化を検討
- [ ] エラー監視用に Sentry を設定（`SENTRY_DSN` と `SENTRY_ENV`）
- [ ] 自動日次バックアップの設定
- [ ] すべてのアカウントに強力でユニークなパスワードを使用
- [ ] Gmail SMTP の場合、通常のパスワードではなくアプリパスワードを使用
- [ ] `./scripts/update.sh` で Go の依存関係を定期的に更新

## バックエンドの更新

新しいバージョンに更新するには：

### Docker の更新

```bash
# 最新のコードを取得
git pull origin main

# Docker イメージのリビルド
docker build -t ministry-mapper-backend .

# 古いコンテナを停止して削除
docker stop ministry-mapper
docker rm ministry-mapper

# 新しいコンテナを起動（ポート 8090 ではなく 8080 を使用）
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

### ローカル開発の更新

```bash
# 最新のコードを取得
git pull origin main

# 依存関係の更新
./scripts/update.sh

# アプリケーションの再起動
./scripts/start.sh
```

**注意**：`pb_data` のデータは更新中も保持されます。

## データベースマイグレーション

バックエンドは開発モード（`go run` を使用）で起動する際にデータベースマイグレーションを自動実行します。ログに以下のようなメッセージが表示されます：

```
Running migrations...
Migration 1759244334_collections_snapshot completed
```

**動作方法：**

- マイグレーションは `migrations/` フォルダに保存されています
- PocketBase は `go run` で実行しているかどうかを検出して自動マイグレーションを実行
- 本番環境（コンパイル済みバイナリ）では、`./main migrate` を使用してマイグレーションを手動で実行する必要があります

**重要：** マイグレーションファイルを手動で削除または修正しないでください。データベースが壊れる可能性があります。

## 監視とログ

### ログの表示

#### Docker ログ

```bash
# 最近のログを表示
docker logs ministry-mapper

# リアルタイムでログをフォロー（デバッグに便利）
docker logs -f ministry-mapper

# 最後の100行を表示
docker logs --tail 100 ministry-mapper
```

#### アプリケーションログ

PocketBase はログを `pb_data/logs/` フォルダに保存します。以下が含まれます：

- リクエストログ
- エラーログ
- カスタムアプリケーションログ

### ログで確認すべき項目

- **起動メッセージ**：正常な初期化を確認（例：「Starting Ministry Mapper build...」）
- **マイグレーションメッセージ**：適用されているデータベースマイグレーション
- **Sentry の初期化**：エラー追跡のセットアップ
- **Cron ジョブ**：バックグラウンドタスクの実行（テリトリー集計、メッセージ処理など）
- **エラーメッセージ**：データベース、メール、API 呼び出し、LaunchDarkly の問題
- **認証イベント**：ログイン試行とユーザーアクティビティ
- **パフォーマンスの問題**：遅いクエリやタイムアウト

## トラブルシューティング

### ポートがすでに使用中

**問題**：「Port already in use」エラー

**解決策**：

```bash
# Docker 用（ポート 8080）
lsof -i :8080

# ローカル開発用（ポート 8090）
lsof -i :8090

# ポートを使用しているプロセスを終了
kill -9 <process_id>

# または別のポートにマップ
docker run -p 8081:8080 ...  # ホストポート 8081 をコンテナポート 8080 にマップ
```

### データベースがロックされている

**問題**：「Database is locked」エラー

**解決策**：
- データベースにアクセスしているすべてのコンテナ/プロセスを停止
- バックアッププロセスが実行中かどうかを確認
- アプリのインスタンスが1つだけ実行されていることを確認
- SQLite は複数プロセスからの同時書き込みをサポートしない
- アプリケーションを再起動

### メールが送信されない

**問題**：ユーザーがメールを受信しない

**解決策**：
1. **SMTP 設定の確認**：`.env` のすべての `PB_SMTP_*` 変数を確認
2. **Gmail ユーザー**：通常のパスワードではなく[アプリパスワード](https://support.google.com/accounts/answer/185833)を使用
3. **スパムフォルダを確認**：メールがフィルタリングされている可能性
4. **SMTP 接続のテスト**：`telnet` などのツールやメールクライアントで SMTP 認証情報を確認
5. **ログを確認**：`pb_data/logs/` または Docker ログのエラーメッセージを確認
6. **ポートの問題**：ポート 587（TLS）または 465（SSL）がファイアウォールでブロックされていないか確認

### メモリ不足

**問題**：メモリエラーでアプリケーションがクラッシュ

**解決策**：
- Docker のメモリ制限を増加：`docker run -m 1g ...`
- メモリリークのログを確認
- 大きなデータベースクエリを最適化
- ホスティングプランの RAM 割り当てをアップグレード
- 大きなデータセット向けの SQLite PRAGMA 最適化を検討

### 管理パネルにアクセスできない

**問題**：管理 URL にアクセスしたときに 404 エラー

**解決策**：
- URL 形式を確認：`http://your-url/_/`（アンダースコアとスラッシュあり）
- `PB_HIDE_CONTROLS=true` かどうかを確認（管理 UI へのアクセスを防止）
- アプリケーションが実行中であることを確認：`docker ps` またはプロセスの確認
- ブラウザキャッシュとクッキーをクリア
- 起動エラーのログを確認

## パフォーマンスのヒント

### 小規模な会衆（ユーザー100人未満）
- デフォルト設定で問題なく動作
- 512MB の RAM で十分
- SQLite はこの負荷を簡単に処理
- 特別な設定は不要

### 中規模の会衆（ユーザー100〜500人）
- 推奨：1GB RAM
- レート制限を有効化：`PB_ENABLE_RATE_LIMITING=true`
- 自動日次バックアップを設定
- `pb_data/data.db` のデータベースサイズを監視

### 大規模な会衆（ユーザー500人以上）
- 推奨：2GB 以上の RAM
- レート制限を有効化
- 専用ホスティングを検討（共有ホスティングではない）
- 遅いクエリのログを監視
- バックアップ保持ポリシーを設定（最後の30日間を保持）
- バックグラウンドジョブの頻度を制御するために LaunchDarkly フィーチャーフラグを検討

## API エンドポイント

バックエンドはこれらのカスタム認証済み API エンドポイントを提供します（すべてユーザー認証が必要）：

### マップ管理
- `POST /map/codes` - 特定のマップのすべてのマップコードを取得
- `POST /map/code/add` - マップに新しい住所コードを追加
- `POST /map/codes/update` - マップコードのシーケンス/順序を更新
- `POST /map/code/delete` - マップから住所コードを削除
- `POST /map/add` - 新しいマップを作成
- `POST /map/territory/update` - マップが属するテリトリーを更新
- `POST /map/reset` - マップをリセット（すべての割り当てとデータをクリア）

### フロア管理
- `POST /map/floor/add` - マップにフロアレベルを追加
- `POST /map/floor/remove` - マップからフロアを削除

### テリトリー管理
- `POST /territory/reset` - テリトリーをリセット（割り当てをクリア）
- `POST /territory/link` - テリトリーのクイックアクセスリンクを作成

### 設定
- `POST /options/update` - 会衆オプション/設定を更新

**認証**：すべてのエンドポイントにリクエストヘッダーに有効な PocketBase 認証トークンが必要です。

**PocketBase 組み込み API**：カスタムエンドポイントに加えて、PocketBase はすべてのコレクション用の自動 REST API を `/api/collections/{collection}/records` で提供します。

## バックグラウンドジョブ（Cron スケジューラー）

バックエンドは cron スケジューラーを使用して自動バックグラウンドタスクを実行します。これらのジョブは LaunchDarkly フィーチャーフラグで制御されます：

### スケジュールされたジョブ

1. **テリトリー集計**（`*/10 * * * *` - 10分ごと）
   - テリトリーの統計と集計データを更新
   - フィーチャーフラグ：`enable-territory-aggregations`

2. **割り当てクリーンアップ**（`*/5 * * * *` - 5分ごと）
   - 期限切れまたは無効なテリトリー割り当てを削除
   - フィーチャーフラグ：`enable-assignments-cleanup`

3. **メッセージ処理**（`*/30 * * * *` - 30分ごと）
   - キューの保留中のメッセージを処理
   - `OPENAI_API_KEY` が設定されている場合、AI による概要をオプションで添付
   - フィーチャーフラグ：`enable-message-processing`

4. **指示処理**（`*/30 * * * *` - 30分ごと）
   - 保留中のテリトリー割り当て指示を処理
   - フィーチャーフラグ：`enable-instruction-processing`

5. **メモ処理**（`0 * * * *` - 毎時）
   - 会衆の更新されたメモを処理
   - `OPENAI_API_KEY` が設定されている場合、AI によるダイジェストをオプションで添付
   - フィーチャーフラグ：`enable-note-processing`

6. **月次レポート**（`0 0 1 * *` - 毎月1日の深夜）
   - 会衆データの Excel レポートを生成して管理者にメール送信
   - フィーチャーフラグ：`enable-monthly-report`
   - AI 要約フラグ：`enable-report-ai-summary`（`OPENAI_API_KEY` が必要）

7. **未プロビジョニングユーザー処理**（`0 1 * * *` - 毎日 01:00 UTC）
   - 役割が割り当てられていないアカウントのユーザーライフサイクルを実施：
     3日目→最初の警告メール、6日目→最終警告メール、7日目→アカウント無効化、37日目以降→永久削除
   - フィーチャーフラグ：`enable-unprovisioned-user-processing`

8. **非アクティブユーザー処理**（`30 1 * * *` - 毎日 01:30 UTC）
   - 設定されたしきい値を超えて非アクティブになったアカウントを警告して最終的に無効化
   - フィーチャーフラグ：`enable-inactive-user-processing`

**注意**：LaunchDarkly が設定されていない場合、すべてのジョブはデフォルトで実行されます（enabled: true）。

### AI レポート要約

`OPENAI_API_KEY` が設定されており、`enable-report-ai-summary` LaunchDarkly フラグがオンの場合、月次 Excel レポートには各テリトリーシートの AI による要約が含まれます。AI はアクティビティデータを分析し、2〜3文の概要と主要な観察事項のリストを生成します。

この機能は完全にオプションです。レポートはこれなしでも完全に機能します。有効にするには：

1. `.env` ファイルに `OPENAI_API_KEY` を設定
2. LaunchDarkly プロジェクトで `enable-report-ai-summary` フラグを有効化（または LaunchDarkly を省略してフラグのデフォルトを有効にする）

## データベースフック

アプリケーションは特定のイベントを自動的に処理します：

- **住所更新**：住所のメモが変更された場合、タイムスタンプとユーザー情報が記録されます
- **住所更新**：マップ集計の再計算をトリガー
- **ユーザー認証**：最終ログインタイムスタンプを更新
- **ユーザー作成**：メールアドレスを正規化して小文字化

## 次のステップ

- [フロントエンドアプリケーション](frontend-setup.md)のセットアップ
- [ユーザー管理](user-management.md)について学ぶ
- [セキュリティ設定](security.md)の設定
