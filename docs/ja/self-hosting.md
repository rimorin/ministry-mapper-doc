# Ministry Mapperのセルフホスティング

!!! warning "セルフホスティングは推奨されません"
    Ministry Mapperのセルフホスティングには、かなりの技術的専門知識と継続的なメンテナンスが必要です。**代わりに[ministry-mapper.com](https://ministry-mapper.com)のホスティングサービスをご利用いただくことをお勧めします。** 以下の条件を満たす場合のみセルフホスティングを進めてください：

    - 経験豊富なシステム管理者または開発者がいる
    - 継続的なメンテナンスと更新のためのリソースがある
    - セキュリティのベストプラクティスを理解している
    - プライバシー規制に準拠する能力がある
    - ホスティングサービスでは満たせない特定の要件がある

## セルフホスティングを推奨しない理由

Ministry Mapperはオープンソースですが、セルフホスティングには課題があります：

- **技術的な複雑さ**：Docker、データベース、ウェブサーバー、クラウドインフラストラクチャの知識が必要
- **セキュリティの責任**：すべてを安全に保ち、更新する責任がある
- **メンテナンスの負担**：定期的な更新、バックアップ、監視が不可欠
- **プライバシーコンプライアンス**：GDPR、CCPAなどのプライバシー法への準拠を確保する必要がある
- **コスト**：サーバーコスト、APIコスト、時間投資がホスティングサービス料金を超えることが多い
- **サポート**：セルフホスティングの問題に対するコミュニティサポートは限られている

## セルフホスティングの前提条件

推奨に反してセルフホスティングを決定した場合は、以下を確認してください：

### 技術要件
- Linuxサーバー管理の経験
- Dockerとコンテナ化の理解
- 環境変数と設定の知識
- ウェブサーバー設定（Nginx/Apache）の知識
- SSL/TLS証明書の経験
- DNSとドメイン設定の理解

### インフラストラクチャ要件
- クラウドホスティングサービス（Railway、Render、DigitalOcean、AWSなど）
- インスタンス用のドメイン名
- 通知用のメールサービス（オプション）
- オプションのAPIコストの予算（Sentryなど）

### 時間のコミットメント
- 初期セットアップ：2〜4時間
- 継続的なメンテナンス：月2〜4時間
- 問題発生時の緊急サポート

## アーキテクチャ概要

Ministry Mapperは2つの独立したコンポーネントで構成されています：

1. **バックエンド（ministry-mapper-be）**：Go拡張機能付きPocketBaseサーバー
   - リポジトリ：[github.com/rimorin/ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)
   - テクノロジー：Go、PocketBase、SQLite
   - ポート8080（Docker）または8090（ローカル）で実行

2. **フロントエンド（ministry-mapper-v2）**：Reactウェブアプリケーション
   - リポジトリ：[github.com/rimorin/ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2)
   - テクノロジー：React、TypeScript、Vite
   - 静的ファイルをホスティングサービスにデプロイ

両方をデプロイして連携するように設定する必要があります。

## バックエンドセルフホスティングガイド

### 環境設定

以下の設定で`.env`ファイルを作成します：

```bash
# アプリケーション設定
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-domain.com

# 初期管理者アカウント（初回ログイン後にパスワードを変更してください！）
PB_ADMIN_EMAIL=admin@your-domain.com
PB_ADMIN_PASSWORD=change_this_secure_password

# メール設定（SMTP）
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PASSWORD=your_app_password
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PORT=587
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper

# セキュリティ設定
PB_ALLOW_ORIGINS=https://your-frontend-domain.com
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true

# 認証（オプション）
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false

# エラー追跡（オプションですが推奨）
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
SOURCE_COMMIT=

# サービス統合（オプション）
MAILERSEND_API_KEY=
MAILERSEND_FROM_EMAIL=
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=
```

### Dockerデプロイメント

1. **リポジトリをクローン**
```bash
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be
```

2. **Dockerイメージをビルド**
```bash
docker build -t ministry-mapper-backend .
```

3. **コンテナを実行**
```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**重要な注意事項：**
- コンテナは内部でポート8080で実行されます
- データを保持するために永続的なボリュームを`/app/pb_data`にマウント
- データベースは`pb_data`フォルダに保存されます

4. **管理パネルにアクセス**
`https://your-backend-domain.com/_/`にアクセスし、管理者資格情報でログインします。

### ローカル開発

ローカルマシンでのテスト用：

```bash
# リポジトリをクローン
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be

# 依存関係をインストール
./scripts/install.sh

# 環境を設定
cp .env.sample .env
# 設定で.envを編集

# アプリケーションを起動
./scripts/start.sh
```

バックエンドはデフォルトで`http://localhost:8090`で実行されます。

### データベースバックアップ

データは重要です。自動バックアップを設定してください：

```bash
# 手動バックアップ
tar -czf backup-$(date +%Y%m%d).tar.gz /path/to/pb_data/

# 自動日次バックアップ（crontabに追加）
0 2 * * * tar -czf /backups/ministry-mapper-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

### セキュリティチェックリスト

- [ ] デフォルトの管理者パスワードを直ちに変更
- [ ] HTTPSのみを使用（HTTPは使用しない）
- [ ] `PB_ALLOW_ORIGINS`を特定のドメインに設定（`*`ではなく）
- [ ] `PB_HIDE_CONTROLS=true`を有効化
- [ ] `PB_ENABLE_RATE_LIMITING=true`を有効化
- [ ] アクセスを制限するファイアウォールを設定
- [ ] 自動バックアップを設定
- [ ] エラー監視用にSentryを設定
- [ ] 強力でユニークなパスワードを使用
- [ ] 依存関係を最新に保つ
- [ ] PocketBaseセキュリティのベストプラクティスを確認

## フロントエンドセルフホスティングガイド

### 環境設定

`.env`ファイルを作成：

```bash
# システム環境
VITE_SYSTEM_ENVIRONMENT=production

# バージョン
VITE_VERSION=$npm_package_version

# PocketBaseバックエンドURL（末尾のスラッシュなし）
VITE_POCKETBASE_URL=https://your-backend-domain.com

# 法的ページ（必須）
VITE_PRIVACY_URL=https://your-domain.com/privacy
VITE_TERMS_URL=https://your-domain.com/terms
VITE_ABOUT_URL=https://your-domain.com/about

# エラー追跡（オプションですが推奨）
VITE_SENTRY_DSN=your_sentry_dsn
```

### 本番用にビルド

```bash
# リポジトリをクローン
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2

# 依存関係をインストール
npm install

# ビルド
npm run build
```

出力は`build/`ディレクトリに配置されます。

### デプロイオプション

#### 静的ホスティングサービス（最も簡単）

**Vercel：**
1. GitHubリポジトリをインポート
2. フレームワーク：Vite
3. ビルドコマンド：`npm run build`
4. 出力ディレクトリ：`build`
5. 環境変数を追加

**Netlify：**
1. Gitから新しいサイト
2. ビルドコマンド：`npm run build`
3. 公開ディレクトリ：`build`
4. 環境変数を追加

**Cloudflare Pages：**
1. Pagesプロジェクトを作成
2. フレームワーク：Vite
3. ビルドコマンド：`npm run build`
4. 出力ディレクトリ：`build`
5. 環境変数を追加

#### セルフホストウェブサーバー

**Nginx設定：**

```nginx
server {
    listen 443 ssl http2;
    server_name your-domain.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    root /var/www/ministry-mapper/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # キャッシュ
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # セキュリティヘッダー
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
}
```

**Apache設定：**

```apache
<VirtualHost *:443>
    ServerName your-domain.com
    DocumentRoot /var/www/ministry-mapper/build

    SSLEngine on
    SSLCertificateFile /path/to/cert.pem
    SSLCertificateKeyFile /path/to/key.pem

    <Directory /var/www/ministry-mapper/build>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.html$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
</VirtualHost>
```

### Sentryセットアップ（オプション）

1. [sentry.io](https://sentry.io)でアカウントを作成
2. Reactプロジェクトを作成
3. DSNを取得
4. `.env`に`VITE_SENTRY_DSN`として追加

## メンテナンスと更新

### 定期メンテナンスタスク

**毎週：**
- Sentryでエラーを確認
- アプリケーションログを確認
- バックアップが機能していることを確認

**毎月：**
- 依存関係を更新：`npm install`および`go get -u`
- セキュリティアドバイザリを確認
- 災害復旧手順をテスト
- ディスク容量の使用状況を確認
- ユーザーフィードバックを確認

**四半期ごと：**
- セキュリティ監査
- パフォーマンスレビュー
- ドキュメントの更新
- プライバシーポリシーの確認と更新
- すべての機能を徹底的にテスト

### 新しいバージョンへの更新

**バックエンド更新：**
```bash
cd ministry-mapper-be
git pull origin main
docker build -t ministry-mapper-backend .
docker stop ministry-mapper
docker rm ministry-mapper
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**フロントエンド更新：**
```bash
cd ministry-mapper-v2
git pull origin main
npm install
npm run build
# build/フォルダをホスティングサービスにデプロイ
```

**更新前に必ずバックアップを取ってください！**

### 監視

**監視すべき項目：**
- サーバーの稼働時間と健全性
- アプリケーションエラー率（Sentry）
- データベースサイズの増加
- バックアップの成功/失敗
- SSL証明書の有効期限
- ユーザー認証の問題
- パフォーマンスメトリクス

**ツール：**
- エラー追跡用のSentry
- API使用量用のGoogle Cloud Console
- サーバー監視（Prometheus、Grafanaなど）
- 稼働時間監視（UptimeRobot、Pingdom）
- ログ集約（ELKスタック、CloudWatch）

## セルフホストインスタンスのトラブルシューティング

### バックエンドの問題

**ポートがすでに使用中：**
```bash
lsof -i :8080
kill -9 <process_id>
```

**データベースがロック：**
- データベースにアクセスしているすべてのインスタンスを停止
- バックエンドプロセスが1つだけ実行されていることを確認
- ハングしているプロセスを確認

**メールが送信されない：**
- SMTP資格情報を確認
- ファイアウォールがSMTPポートをブロックしていないか確認
- Gmailの場合はアプリパスワードを使用
- `pb_data/logs/`のメールログを確認

**管理パネルにアクセスできない：**
- URLは`https://your-domain.com/_/`である必要がある（アンダースコアに注意）
- `PB_HIDE_CONTROLS`設定を確認
- バックエンドが実行中であることを確認
- ブラウザキャッシュをクリア

### フロントエンドの問題

**バックエンドに接続できない：**
- `VITE_POCKETBASE_URL`が正しいことを確認
- バックエンドのCORS設定を確認
- バックエンドがアクセス可能であることを確認
- ブラウザコンソールでエラーを確認

**ビルドの失敗：**
- Node.jsバージョン22以上を確認
- `node_modules`を削除して再インストール
- すべての環境変数が設定されていることを確認
- ビルドエラーメッセージを確認

### パフォーマンスの問題

**応答時間が遅い：**
- サーバーリソース（CPU、RAM、ディスク）を確認
- データベースサイズとクエリを確認
- データベース最適化を有効化
- ネットワークレイテンシを確認
- 遅いクエリについてPocketBaseログを確認

## プライバシーと法的コンプライアンス

### セルフホスターとしてのあなたの責任

セルフホスティングの場合、あなたはデータ管理者であり、以下を行う必要があります：

- **プライバシー法を遵守**：GDPR（EU）、CCPA（カリフォルニア）など
- **プライバシーポリシーを作成**：データ収集と使用を説明
- **利用規約を作成**：ユーザーの責任を定義
- **データセキュリティを実装**：暗号化、アクセス制御、バックアップ
- **データリクエストを処理**：ユーザーのアクセス、削除、ポータビリティの権利
- **データ侵害を報告**：法定期限内に
- **記録を維持**：データ処理活動
- **DPOを任命**：規制で要求される場合

### 必要な法的文書

以下を提供する必要があります：

1. **プライバシーポリシー** - 法律で必要、以下を説明する必要があります：
   - 収集するデータ
   - 使用方法
   - 保持期間
   - ユーザーの権利
   - 連絡方法

2. **利用規約** - 以下を定義：
   - 許容される使用
   - ユーザーの責任
   - 責任の制限
   - 終了ポリシー

3. **Cookieポリシー** - Cookie/トラッキングを使用する場合

### データ保護対策

- あらゆる場所でHTTPSを使用
- 保存データを暗号化
- アクセス制御を実装
- 定期的なセキュリティ監査
- バックアップの暗号化
- 安全なパスワードポリシー
- 多要素認証
- アクティビティログ
- 定期的なセキュリティ更新

## コストの考慮事項

### インフラストラクチャコスト

**バックエンドホスティング：**
- VPS：月額$10〜50（DigitalOcean、Linode）
- PaaS：月額$10〜30（Railway、Render）
- クラウド：変動（AWS、GCP）

**フロントエンドホスティング：**
- 静的ホスティング：月額$0〜10（Vercel、Netlify、Cloudflare）
- CDNコスト：通常含まれる

**ドメイン：**
- 年間$10〜20

**SSL証明書：**
- 無料（Let's Encrypt）または年間$0〜100

### サービスコスト

**メールサービス：**
- Gmail SMTP：無料（制限あり）
- MailerSend：無料枠あり
- SendGrid：無料枠あり

**エラー追跡：**
- Sentry：無料枠あり
- 有料：月額$26〜80

**バックアップストレージ：**
- サイズに応じて月額$5〜20

### 時間投資

**初期セットアップ：**
- バックエンド：2〜3時間
- フロントエンド：1〜2時間
- 設定：1〜2時間
- テスト：2〜3時間
- **合計：6〜10時間**

**月次メンテナンス：**
- 監視：1〜2時間
- 更新：1〜2時間
- サポート：1〜3時間
- **合計：3〜7時間**

### 総所有コスト（年間）

**小規模会衆（ユーザー100人未満）：**
- インフラストラクチャ：$240〜720
- API：$0〜100（通常無料枠）
- サービス：$0〜300
- ドメイン/SSL：$10〜20
- **合計：$250〜1,140 + 年間48〜96時間**

コミットする前にホスティングサービスのサブスクリプションと比較してください。

## ヘルプを得る

### コミュニティサポート

- GitHub Issues：[ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2/issues)および[ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be/issues)
- 新しいイシューを作成する前に既存のイシューを読む
- 問題を報告する際は詳細な情報を提供

### サポートリクエストに含めるべき内容

1. バージョン番号（バックエンドとフロントエンド）
2. デプロイメント環境（Docker、ローカル、ホスティングサービス）
3. エラーメッセージ（完全なテキスト）
4. 再現手順
5. 期待される動作と実際の動作
6. 関連ログ
7. 設定（サニタイズ済み、シークレットなし）

### 限定サポート

!!! info "サポートの制限"
    セルフホスティングサポートはコミュニティベースで限定されています。Ministry Mapperチームはホスティングサービスを優先しています。プロフェッショナルサポートについては、代わりにホスティングサービスの使用を検討してください。

## セルフホスティングの代替案

セルフホスティングの前に、以下を検討してください：

1. **[ministry-mapper.com](https://ministry-mapper.com)の公式ホスティングサービスを使用**
   - メンテナンスの負担なし
   - プロフェッショナルサポート
   - 自動更新
   - より良い信頼性
   - コンプライアンスは私たちが処理
   - 今すぐ利用可能！

2. **機能をリクエスト**
   - カスタマイズが必要な場合は、メインプロジェクトで機能をリクエスト
   - 全員に利益をもたらす
   - チームによって維持される
   - ホスティングサービスに追加される可能性がある

3. **プロジェクトに貢献**
   - メインコードベースを改善
   - 他の人を助ける
   - コミュニティサポートを得る

## まとめ

Ministry Mapperのセルフホスティングは可能ですが、かなりの専門知識、時間、リソースが必要です。**追加の複雑さを正当化する特定の要件がない限り、[ministry-mapper.com](https://ministry-mapper.com)のホスティングサービスの使用を強くお勧めします。**

### 代わりにホスティングサービスを使用

**[ministry-mapper.com](https://ministry-mapper.com)**にアクセスして：
- インスタントセットアップ - 技術的な知識は不要
- 必要なときのプロフェッショナルサポート
- 自動バックアップとセキュリティ更新
- 法的コンプライアンスの支援
- より良い信頼性と稼働時間
- 総所有コストの低減

### それでもセルフホストしたい場合

セルフホスティングを進める場合は：
- すべてのセキュリティのベストプラクティスに従う
- システムを最新に保つ
- 定期的なバックアップを維持
- 法的コンプライアンスを確保
- システムの健全性を監視
- 継続的なコストの予算を組む
- メンテナンスの時間を確保する

このガイド全体を読み、影響を理解していることを確認してください。幸運を祈ります！
