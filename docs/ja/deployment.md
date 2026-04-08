# デプロイメントガイド

## 概要

このガイドでは、ホストサービスの使用から高度なセルフホスト設定まで、Ministry Mapper のさまざまなデプロイオプションについて説明します。

## クイックデプロイ決定ツリー

```
┌─────────────────────────────────────┐
│ 技術的な専門知識と                    │
│ セルフホストの特定のニーズがありますか？│
└──────────────┬──────────────────────┘
               │
       ┌───────┴───────┐
       │               │
      はい             いいえ
       │               │
       │               └─────────────────┐
       │                                 │
       ▼                                 ▼
┌──────────────┐              ┌──────────────────┐
│ セルフホスト  │              │ ホストサービス    │
│              │              │ を使用           │
│ 下記を参照   │              │                  │
└──────────────┘              │ ministry-mapper  │
                              │ .com             │
                              └──────────────────┘
```

## オプション1：ホストサービス（推奨）

**最適：** ほとんどの会衆、中小規模の組織

**URL：** [ministry-mapper.com](https://ministry-mapper.com)

### 利点

✅ **ゼロセットアップ** - すぐに使用開始
✅ **プロフェッショナルサポート** - 必要なときにヘルプ
✅ **自動更新** - 常に最新バージョン
✅ **維持されたインフラ** - サーバー管理不要
✅ **より良いセキュリティ** - プロフェッショナルなセキュリティ慣行
✅ **コンプライアンス支援** - GDPR、CCPA などのヘルプ
✅ **バックアップ含む** - 自動データバックアップ
✅ **スケーラビリティ** - 成長に自動対応
✅ **コスト効率** - セルフホストより安いことが多い

### はじめかた

1. [ministry-mapper.com](https://ministry-mapper.com) にアクセス
2. アカウントを作成
3. メールを確認
4. 会衆アクセスのための管理者に連絡
5. すぐに使用開始

**技術的な知識は不要です！**

---

## オプション2：セルフホスト

**最適：** 技術的な専門知識と特定の要件を持つ組織

**要件：**
- 経験豊富なシステム管理者または開発者
- Docker、データベース、Webサーバーの理解
- システムを維持・更新する能力
- セキュリティのベストプラクティスの知識
- プライバシー法のコンプライアンス専門知識

### セルフホストの概要

Ministry Mapper は2つのコンポーネントで構成されています：

1. **バックエンド** - SQLite を使用した PocketBase Go アプリケーション
2. **フロントエンド** - React 静的 Web アプリケーション

両方をデプロイして接続する必要があります。

---

## バックエンドデプロイオプション

### オプション1：Railway（セルフホストに推奨）

**最適：** 最小限の設定で簡単なセルフホスト

**セットアップ手順：**

1. **Railway アカウントの作成**
   ```
   Visit: https://railway.app
   GitHub でサインアップ
   ```

2. **バックエンドのデプロイ**
   ```bash
   # リポジトリをフォーク
   git clone https://github.com/rimorin/ministry-mapper-be
   cd ministry-mapper-be
   
   # Railway に接続
   railway init
   railway link
   ```

3. **環境の設定**
   ```bash
   # Railway ダッシュボードで環境変数を追加
   PB_APP_URL=https://your-frontend.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password_here
   SENTRY_DSN=your_sentry_dsn
   ```

4. **デプロイ**
   ```bash
   railway up
   ```

**機能：**
- 無料ティアあり
- 自動 HTTPS
- 簡単なスケーリング
- 組み込み監視
- ワンクリックデプロイ

---

### オプション2：Render

**セットアップ手順：**

1. **Render アカウントの作成**
   ```
   Visit: https://render.com
   サインアップ
   ```

2. **新しい Web サービスの作成**
   - GitHub リポジトリを接続
   - `ministry-mapper-be` を選択
   - Docker デプロイを選択

3. **環境の設定**
   ```
   PB_APP_URL=https://your-frontend-url.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password
   PB_ALLOW_ORIGINS=https://your-frontend-url.vercel.app
   ```

4. **デプロイ**
   - Render が自動的にビルドとデプロイを実行
   - 初回デプロイには5〜10分かかります

**機能：**
- 制限付き無料ティア
- 自動 HTTPS
- 簡単なスケーリング
- GitHub 統合

---

### オプション3：DigitalOcean Droplet

**最適：** 完全な制御、本番デプロイ

**セットアップ手順：**

1. **Droplet の作成**
   ```bash
   # Ubuntu 22.04 LTS 推奨
   # 最小：1GB RAM、1 CPU、25GB SSD
   # 推奨：2GB RAM、2 CPU、50GB SSD
   ```

2. **Docker のインストール**
   ```bash
   sudo apt update
   sudo apt install docker.io docker-compose -y
   sudo systemctl enable docker
   sudo systemctl start docker
   ```

3. **リポジトリのクローン**
   ```bash
   git clone https://github.com/rimorin/ministry-mapper-be.git
   cd ministry-mapper-be
   ```

4. **環境の設定**
   ```bash
   cp .env.sample .env
   nano .env  # 設定を編集
   ```

5. **ビルドと実行**
   ```bash
   docker-compose up -d
   ```

6. **Nginx リバースプロキシのセットアップ**
   ```nginx
   server {
       listen 80;
       server_name api.your-domain.com;
   
       location / {
           proxy_pass http://localhost:8080;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```

7. **Certbot で SSL をセットアップ**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d api.your-domain.com
   ```

**機能：**
- 完全な制御
- 予測可能な料金（$6〜12/月）
- 高パフォーマンス
- カスタム設定

---

### オプション4：AWS（上級）

**最適：** エンタープライズデプロイ、高いスケーラビリティのニーズ

**アーキテクチャ：**
```
Route 53（DNS）
    ↓
CloudFront（CDN）
    ↓
Application Load Balancer
    ↓
ECS Fargate（コンテナ）
    ↓
RDS PostgreSQL（SQLite から移行する場合）
    ↓
S3（ファイルストレージ）
```

**セットアップ概要：**

1. **ECS クラスターの作成**
2. **Docker イメージのビルドと ECR へのプッシュ**
3. **タスク定義の作成**
4. **ロードバランサーの設定**
5. **CloudFront CDN のセットアップ**
6. **Route 53 DNS の設定**

**費用：** 使用量によって月額 $50〜200

**複雑さ：** 高 - AWS の専門知識が必要

---

## フロントエンドデプロイオプション

### オプション1：Vercel（推奨）

**最適：** 優れた DX での高速で簡単なデプロイ

**セットアップ手順：**

1. **Vercel アカウントの作成**
   ```
   Visit: https://vercel.com
   GitHub でサインアップ
   ```

2. **リポジトリのインポート**
   ```
   - 「New Project」をクリック
   - ministry-mapper-v2 をインポート
   - フレームワーク：Vite
   - ビルドコマンド：npm run build
   - 出力ディレクトリ：build
   ```

3. **環境変数の設定**
   ```bash
   VITE_SYSTEM_ENVIRONMENT=production
   VITE_VERSION=$npm_package_version
   VITE_OPENROUTE_API_KEY=your_key
   VITE_LOCATIONIQ_API_KEY=your_key
   VITE_POCKETBASE_URL=https://your-backend-url.com
   VITE_PRIVACY_URL=https://your-site.com/privacy
   VITE_TERMS_URL=https://your-site.com/terms
   VITE_SENTRY_DSN=your_sentry_dsn
   ```

4. **デプロイ**
   - Vercel はプッシュ時に自動デプロイ
   - 2〜3分かかります

**機能：**
- 個人プロジェクトは無料
- 自動 HTTPS
- エッジネットワーク（グローバルに高速）
- PR のプレビューデプロイ
- カスタムドメイン

---

### オプション2：Netlify

**セットアップ手順：**

1. **Netlify アカウントの作成**
2. **リポジトリのインポート**
   - GitHub を接続
   - `ministry-mapper-v2` を選択
3. **ビルド設定**
   ```
   ビルドコマンド：npm run build
   公開ディレクトリ：build
   ```
4. **環境変数の追加**
5. **デプロイ**

**機能：**
- 無料ティア
- 自動 HTTPS
- フォーム処理
- サーバーレス関数

---

### オプション3：Cloudflare Pages

**セットアップ手順：**

1. **Cloudflare アカウントの作成**
2. **Pages プロジェクトの作成**
   - GitHub を接続
   - リポジトリを選択
3. **ビルド設定**
   ```
   フレームワーク：Vite
   ビルドコマンド：npm run build
   出力ディレクトリ：build
   ```
4. **環境変数**
5. **デプロイ**

**機能：**
- 無制限の帯域幅
- グローバル CDN
- DDoS 保護
- Web 分析

---

### オプション4：AWS S3 + CloudFront

**最適：** AWS 中心のデプロイ

**セットアップ手順：**

1. **S3 バケットの作成**
   ```bash
   aws s3 mb s3://ministry-mapper-frontend
   ```

2. **静的ホスティングの設定**
   ```bash
   aws s3 website s3://ministry-mapper-frontend \
     --index-document index.html \
     --error-document index.html
   ```

3. **ビルドのアップロード**
   ```bash
   npm run build
   aws s3 sync build/ s3://ministry-mapper-frontend
   ```

4. **CloudFront ディストリビューションの作成**
   - オリジン：S3 バケット
   - ビューアープロトコル：HTTP を HTTPS にリダイレクト
   - 価格クラス：すべてのエッジロケーションを使用

5. **カスタムドメインの設定**

**費用：** 小規模サイトで約 $1〜5/月

---

## 環境設定

### バックエンド環境変数

**必須：**
```bash
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-url.com
PB_ADMIN_EMAIL=admin@example.com
PB_ADMIN_PASSWORD=secure_password
PB_ALLOW_ORIGINS=https://your-frontend-url.com
```

**SMTP（メール）：**
```bash
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PORT=587
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PASSWORD=app_password
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper
```

**セキュリティ：**
```bash
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false
```

**監視：**
```bash
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
```

**オプションサービス：**
```bash
MAILERSEND_API_KEY=your_key
LAUNCHDARKLY_SDK_KEY=your_key
```

### フロントエンド環境変数

**必須：**
```bash
VITE_SYSTEM_ENVIRONMENT=production
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=https://your-backend.com
```

**法的ページ：**
```bash
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about
```

**オプション API：**
```bash
VITE_OPENROUTE_API_KEY=your_openrouteservice_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_key
```

**監視：**
```bash
VITE_SENTRY_DSN=your_sentry_dsn
```

---

## SSL/TLS 証明書のセットアップ

### オプション1：Let's Encrypt（無料）

**Certbot を使用：**
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

**自動更新：**
```bash
sudo certbot renew --dry-run
```

### オプション2：Cloudflare SSL

- Cloudflare に含まれる無料 SSL
- 自動更新
- ユニバーサル SSL

### オプション3：クラウドプロバイダー SSL

ほとんどのクラウドプロバイダー（Vercel、Netlify、Railway）は自動 SSL を含みます

---

## データベースバックアップ戦略

### 自動バックアップ

**日次バックアップスクリプト：**
```bash
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups"
DB_PATH="/app/pb_data/data.db"

# バックアップを作成
cp $DB_PATH $BACKUP_DIR/backup_$DATE.db

# 圧縮
gzip $BACKUP_DIR/backup_$DATE.db

# 最後の30日間を保持
find $BACKUP_DIR -name "backup_*.gz" -mtime +30 -delete
```

**Cron スケジュール：**
```bash
0 2 * * * /path/to/backup-script.sh
```

### クラウドバックアップ

**AWS S3：**
```bash
aws s3 sync /backups/ s3://your-bucket/ministry-mapper-backups/
```

**Render：**
- 自動バックアップ含む
- 日次スナップショット
- 7日間の保持

---

## 監視とロギング

### Sentry のセットアップ

1. [sentry.io](https://sentry.io) でアカウントを作成
2. 新しいプロジェクトを作成（React + Go）
3. DSN をコピー
4. 環境変数に追加
5. アラートを設定

**メリット：**
- リアルタイムエラー追跡
- パフォーマンス監視
- リリース追跡
- ユーザーフィードバック

### アプリケーション監視

**追跡するメトリクス：**
- エラー率
- レスポンスタイム
- データベースサイズ
- アクティブユーザー
- API 呼び出し
- メモリ使用量
- ディスクスペース

**ツール：**
- Grafana + Prometheus
- Datadog
- New Relic
- クラウドプロバイダーの監視

---

## セキュリティのベストプラクティス

### バックエンドセキュリティ

1. **強力なパスワードを使用**
   - 最小12文字
   - 文字、数字、記号を混在

2. **レート制限を有効化**
   ```bash
   PB_ENABLE_RATE_LIMITING=true
   ```

3. **CORS の設定**
   ```bash
   PB_ALLOW_ORIGINS=https://your-frontend-url.com
   ```

4. **定期的な更新**
   ```bash
   # 毎月更新を確認
   docker pull your-image:latest
   ```

5. **ファイアウォールルール**
   ```bash
   # ポート 80、443、22（SSH）のみ許可
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   sudo ufw allow 22/tcp
   sudo ufw enable
   ```

### フロントエンドセキュリティ

1. **環境変数**
   - `.env` ファイルをコミットしない
   - プラットフォームの環境変数 UI を使用

2. **API キーの制限**
   - API キーを特定の API に制限

3. **コンテンツセキュリティポリシー**
   ```html
   <meta http-equiv="Content-Security-Policy" 
         content="default-src 'self'; 
                  script-src 'self' 'unsafe-inline'; 
                  style-src 'self' 'unsafe-inline';">
   ```

4. **HTTPS のみ**
   - 本番環境で HTTPS を強制
   - HSTS ヘッダーを設定

---

## パフォーマンス最適化

### バックエンド最適化

1. **Gzip 圧縮を有効化**
2. **データベースインデックス**（すでに設定済み）
3. **コネクションプーリング**
4. **キャッシングヘッダー**

### フロントエンド最適化

1. **コード分割**（Vite で自動）
2. **画像最適化**
3. **静的アセット用 CDN**
4. **サービスワーカーキャッシング**

**Lighthouse スコア目標：**
- パフォーマンス：>90
- アクセシビリティ：>95
- ベストプラクティス：>95
- SEO：>90

---

## スケーラビリティの考慮事項

### 現在の制限

- SQLite シングルライター
- 同時接続ユーザー1000人未満に適合
- 中小規模の会衆に適切

### スケーリングオプション

**垂直スケーリング：**
- サーバーリソースの増加
- より多くの RAM、CPU、ストレージ

**水平スケーリング：**
- PocketBase 読み取りレプリカ
- フロントエンド用 CDN
- ロードバランサー

**データベース移行：**
- より大きなスケール用に PostgreSQL に移行
- PocketBase は PostgreSQL をサポート

---

## デプロイのトラブルシューティング

### バックエンドの問題

**問題：** データベースがロックされている
**解決策：** 実行中のインスタンスが1つだけであることを確認

**問題：** メールが送信されない
**解決策：** SMTP 認証情報とポートを確認

**問題：** メモリ使用量が多い
**解決策：** サーバー RAM を増加するかクエリを最適化

### フロントエンドの問題

**問題：** ビルドが失敗する
**解決策：** Node.js バージョンを確認（22+ が必要）

**問題：** CORS エラー
**解決策：** バックエンドで `PB_ALLOW_ORIGINS` を設定

---

## コスト見積もり

### ホストサービス
- 料金については ministry-mapper.com にお問い合わせ
- サポート、更新、バックアップが含まれます

### セルフホストのコスト

**最小限のセットアップ：**
- Railway/Render：$0〜5/月（無料ティア）
- Vercel/Netlify：$0/月（無料ティア）
- **合計：$0〜5/月**

**推奨セットアップ：**
- バックエンド（Railway）：$5/月
- フロントエンド（Vercel）：$0/月
- Sentry：$0/月（無料ティア）
- **合計：$5/月**

**本番セットアップ：**
- DigitalOcean Droplet：$12/月
- CloudFront CDN：$2/月
- Sentry Pro：$26/月
- **合計：$40/月**

**エンタープライズセットアップ：**
- AWS インフラ：$100〜500/月
- サポート契約：変動
- **合計：$100〜500+/月**

**時間投資：**
- 初期セットアップ：4〜8時間
- 月次メンテナンス：2〜4時間
- 緊急サポート：随時

---

## 次のステップ

### デプロイ後

1. **徹底的なテスト**
   - テストアカウントを作成
   - サンプルテリトリーを作成
   - すべての機能をテスト
   - メールが機能することを確認

2. **ユーザートレーニング**
   - ドキュメントを作成
   - 管理者をトレーニング
   - ユーザーセッションを開催

3. **システムの監視**
   - Sentry を毎日確認
   - ログを毎週確認
   - 毎月更新

4. **バックアップの検証**
   - 復元プロセスをテスト
   - バックアップの整合性を検証
   - 手順を文書化

---

## サポートリソース

### ドキュメント
- [アーキテクチャ概要](architecture.md)
- [バックエンドセットアップ](backend-setup.md)
- [フロントエンドセットアップ](frontend-setup.md)

### コミュニティ
- GitHub Issues
- ドキュメントサイト
- コミュニティフォーラム

### プロフェッショナルサポート
- ホストサービスサポート
- コンサルティングあり
- カスタム開発
