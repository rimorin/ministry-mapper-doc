# フロントエンドセットアップガイド

!!! warning "セルフホスティングは推奨されません"
    このガイドは、Ministry Mapperをセルフホストしたい上級ユーザー向けです。技術的な複雑さ、保守の負担、セキュリティの責任を考慮すると、セルフホスティングはお勧めしません。

    **ほとんどのユーザーは**：**[ministry-mapper.com](https://ministry-mapper.com)**のホスティングサービスをご利用ください

!!! info "ユーザードキュメントをお探しですか？"
    Ministry Mapperの使用方法を学びたい一般ユーザーの方は、代わりに[はじめに](getting-started.md)または[ユーザーガイド](user-guide.md)をご覧ください。

## 概要

Ministry Mapperフロントエンド（ministry-mapper-v2）は、以下を提供するReactベースのウェブアプリケーションです：

- ユーザー認証とアカウント管理
- インタラクティブな区域管理インターフェース
- インタラクティブなマッピング機能
- リアルタイムデータ同期
- モバイルレスポンシブデザイン
- 多言語サポート（7言語）
- プログレッシブウェブアプリ機能

**このガイドは以下を前提としています**：
- Node.jsとnpmの経験
- 環境変数の理解
- 静的サイトデプロイの知識
- ウェブアプリケーションセキュリティの知識

完全なセルフホスティング手順については、[セルフホスティングガイド](self-hosting.md)を参照してください。

## クイックリファレンス

このページでは、フロントエンド設定のクイックリファレンスを提供します。**完全なセットアップ手順については、[セルフホスティングガイド](self-hosting.md)を参照してください。**

## クイックリファレンス

このページでは、フロントエンド設定のクイックリファレンスを提供します。**完全なセットアップ手順については、[セルフホスティングガイド](self-hosting.md)を参照してください。**

### テクノロジースタック

- **React 19**：モダンなUIライブラリ
- **TypeScript**：型安全なJavaScript
- **Vite 7**：高速ビルドツールと開発サーバー
- **Bootstrap 5**：レスポンシブCSSフレームワーク
- **Leaflet**：インタラクティブマッピング
- **PocketBase SDK**：バックエンド接続
- **Sentry**：エラー追跡
- **i18next**：多言語サポート
- **Vite PWA**：プログレッシブウェブアプリ機能

---

!!! note "完全なセットアップ手順"
    以下のセクションでは、フロントエンド設定の技術リファレンスを提供します。デプロイオプションとトラブルシューティングを含むステップバイステップのセットアップ手順については、**[セルフホスティングガイド](self-hosting.md)**を参照してください。

---

## 環境変数

ルートディレクトリに以下の設定で`.env`ファイルを作成します：

### 必須変数

```bash
# システム環境 - デプロイメント環境を指定
VITE_SYSTEM_ENVIRONMENT=production

# バージョン - package.jsonのバージョンを自動的に使用
VITE_VERSION=$npm_package_version

# PocketBaseバックエンドURL - 末尾のスラッシュなし
VITE_POCKETBASE_URL=https://your-backend-url.com

# 法的ページ - 本番環境では必須
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about
```

### オプション変数

```bash
# エラー追跡（Sentry）- 本番環境では推奨
VITE_SENTRY_DSN=https://your_sentry_dsn@sentry.io/123456
# ソースマップアップロード用のビルド時Sentryトークン
SENTRY_AUTH_TOKEN=your_sentry_auth_token
SENTRY_ORG=your_sentry_org_slug
SENTRY_PROJECT=your_sentry_project_slug

# ルーティングとジオコーディング
VITE_OPENROUTE_API_KEY=your_openrouteservice_api_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_api_key

# メンテナンスモード - ユーザーにメンテナンスバナーを表示
VITE_MAINTENANCE_MODE=false
```

### 変数の詳細

#### VITE_SYSTEM_ENVIRONMENT

- **目的**：アプリが実行されている環境を決定
- **値**：
  - `local` - ローカルマシンでの開発
  - `production` - 本番デプロイメント
- **効果**：Sentryエラー追跡設定とロギングに影響

#### VITE_VERSION

- **目的**：エラーレポートでアプリケーションバージョンを追跡
- **値**：`$npm_package_version`（package.jsonから自動的に読み取り）
- **現在のバージョン**：1.9.1（package.json時点）
- **効果**：Sentryレポートに表示され、どのバージョンに問題があるかを追跡するのに役立つ

#### VITE_POCKETBASE_URL

- **目的**：PocketBaseバックエンドサーバーのURL
- **形式**：末尾のスラッシュなしの完全なURL
- **例**：`https://api.ministry-mapper.com`またはローカル用に`http://localhost:8090`
- **重要**：フロントエンドがデプロイされている場所からアクセス可能である必要があります

#### VITE_PRIVACY_URL

- **目的**：プライバシーポリシーページへのリンク
- **必須**：はい、法的コンプライアンス（GDPR、CCPAなど）のため
- **例**：`https://ministry-mapper.com/privacy`

#### VITE_TERMS_URL

- **目的**：利用規約ページへのリンク
- **必須**：はい、法的コンプライアンスのため
- **例**：`https://ministry-mapper.com/terms`

#### VITE_ABOUT_URL

- **目的**：Ministry Mapperインスタンスに関する情報へのリンク
- **必須**：いいえ、ただし推奨
- **例**：`https://ministry-mapper.com/about`

#### VITE_SENTRY_DSN

- **目的**：監視のためにJavaScriptエラーをSentryに送信
- **取得先**：[sentry.io](https://sentry.io)（無料枠あり）
- **必須**：いいえ、ただし本番環境では強く推奨
- **利点**：エラーを追跡、パフォーマンスを監視、問題の通知を受ける
- **注意**：VITE_SYSTEM_ENVIRONMENTが「production」の場合のみアクティブ

#### SENTRY_AUTH_TOKEN / SENTRY_ORG / SENTRY_PROJECT

- **目的**：`npm run build`中にソースマップをSentryにアップロードし、縮小されたスタックトレースを元のソースコードに解決できるようにするために使用
- **必須**：いいえ — これらがない場合、ソースマップはローカルで生成されますが、Sentryにアップロードすると本番環境でのエラーデバッグが大幅に改善されます
- **取得方法**：Sentry組織設定の**Settings → Auth Tokens**で認証トークンを作成

#### VITE_OPENROUTE_API_KEY

- **目的**：インタラクティブマップでのターンバイターンのルーティングと道順案内を実行
- **取得先**：[openrouteservice.org](https://openrouteservice.org)（無料枠あり）
- **必須**：いいえ — マップナビゲーションはこれなしで機能しますが、道順案内は利用できません

#### VITE_LOCATIONIQ_API_KEY

- **目的**：ジオコーディング（住所を座標に変換）と逆ジオコーディング（座標を住所に変換）
- **取得先**：[locationiq.com](https://locationiq.com)（無料枠あり）
- **必須**：いいえ — アプリはこれなしで機能しますが、住所検索と自動入力は利用できません

#### VITE_MAINTENANCE_MODE

- **目的**：`true`に設定すると、ユーザーにメンテナンスバナーを表示
- **値**：`true`または`false`
- **デフォルト**：`false`
- **使用例**：バックエンドのアップグレードや計画的なダウンタイム中に一時的に`true`に設定

## ローカル開発セットアップ

### 前提条件

Node.jsバージョン24以上が必要です：

```bash
# Node.jsバージョンを確認
node --version
```

Node.js 24以上をお持ちでない場合は、[nodejs.org](https://nodejs.org)からダウンロードしてください。

### セットアップ手順

#### 1. リポジトリをクローン

```bash
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2
```

#### 2. 依存関係をインストール

```bash
npm install
```

これにより、必要なすべてのパッケージがダウンロードされます。インターネット接続によっては数分かかる場合があります。

#### 3. 環境ファイルを作成

```bash
# 環境ファイルの例をコピー
cp .env.example .env
```

`.env`を設定で編集：

```bash
VITE_SYSTEM_ENVIRONMENT=local
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=http://localhost:8090
VITE_PRIVACY_URL=http://localhost:3000/privacy
VITE_TERMS_URL=http://localhost:3000/terms
VITE_ABOUT_URL=http://localhost:3000/about
VITE_SENTRY_DSN=
VITE_OPENROUTE_API_KEY=
VITE_LOCATIONIQ_API_KEY=
VITE_MAINTENANCE_MODE=false
```

**注意**：ポート8090でPocketBaseバックエンドが実行されている必要があります。バックエンドのセットアップについては、ministry-mapper-beリポジトリを参照してください。

#### 4. 開発サーバーを起動

```bash
npm start
```

アプリは`http://localhost:3000`で開きます（ポート3000はvite.config.jsで設定されています）。

### 開発機能

- **ホットモジュールリプレースメント（HMR）**：変更が完全なページリロードなしで即座に反映
- **TypeScriptチェック**：エラーがターミナルとブラウザコンソールに表示
- **ソースマップ**：元のソースコード参照でデバッグが容易に
- **Fast Refresh**：Reactコンポーネントが状態を失わずに更新

### 開発コマンド

```bash
# 開発サーバーを起動（ポート3000）
npm start

# Vitestでテストを実行
npm test

# コードフォーマットをチェック
npm run prettier

# フォーマットの問題を自動修正
npm run prettier:fix

# 本番用にビルド
npm run build

# 本番ビルドをローカルでプレビュー
npm run serve
```

### コード品質ツール

**Prettier**（コードフォーマット）：

- `.prettierrc`で設定
- Huskyを介してコミット時に自動フォーマット
- `npm run prettier:fix`で手動実行

**ESLint**（コードリンティング）：

- `eslint.config.mjs`で設定
- ReactとTypeScriptのルールが有効
- 一般的なエラーとコード品質の問題をチェック

**Husky**（gitフック）：

- コミット前にステージングされたファイルでPrettierを実行
- 一貫したコードフォーマットを確保
- commitlintでコミットメッセージを検証

## 本番デプロイメント

### 本番用にビルド

```bash
# 最適化された本番ファイルをビルド
npm run build
```

**ビルド中に起こること：**

- TypeScriptがJavaScriptにコンパイル
- Reactコードが最適化され、縮小
- CSSが処理され、縮小
- ソースマップが生成
- コードが高速な読み込みのためにチャンクに分割
- 出力が`build/`ディレクトリに移動
- PWAサポート用のサービスワーカーが生成

**ビルド出力：**

- `build/index.html` - メインHTMLファイル
- `build/assets/` - JavaScript、CSS、その他のアセット
- ファイル名にはキャッシング用のコンテンツハッシュが含まれる
- 合計サイズは通常1MB未満（外部依存関係を除く）

### デプロイオプション

Ministry Mapperフロントエンドは静的ウェブアプリケーションです。任意の静的ホスティングサービスにデプロイできます。

#### オプション1：Vercel（推奨）

高速、無料枠、自動デプロイメント。

**セットアップ：**

1. [vercel.com](https://vercel.com)でアカウントを作成
2. 「New Project」をクリック
3. GitHubリポジトリをインポート
4. 設定：
   - Framework Preset：Vite
   - Build Command：`npm run build`
   - Output Directory：`build`
   - Install Command：`npm install`
5. Vercelダッシュボードで環境変数を追加
6. デプロイ

**Vercelの機能：**

- gitプッシュで自動デプロイ
- プルリクエストのプレビューデプロイ
- カスタムドメイン
- デフォルトでHTTPS
- グローバルCDN
- 個人プロジェクトは無料

#### オプション2：Netlify

優れた機能を備えたシンプルなデプロイメント。

**セットアップ：**

1. [netlify.com](https://netlify.com)でアカウントを作成
2. 「Add new site」→「Import an existing project」
3. GitHubリポジトリを接続
4. ビルド設定：
   - Build Command：`npm run build`
   - Publish Directory：`build`
5. 環境変数を追加
6. デプロイ

**Netlifyの機能：**

- 自動デプロイメント
- フォーム処理
- サーバーレス関数（必要に応じて）
- カスタムドメイン
- デフォルトでHTTPS

#### オプション3：Cloudflare Pages

寛大な無料枠を備えた高速CDN。

**セットアップ：**

1. [cloudflare.com](https://cloudflare.com)でアカウントを作成
2. Pages →「Create a project」に移動
3. GitHubリポジトリを接続
4. ビルド設定：
   - Framework preset：Vite
   - Build command：`npm run build`
   - Build output directory：`build`
5. 環境変数を追加
6. デプロイ

**Cloudflareの機能：**

- グローバルCDNネットワーク
- DDoS保護
- アナリティクス
- クライアントサイドトラッキングなしのWeb Analytics

#### オプション4：AWS S3 + CloudFront

より多くの制御、大規模なデプロイメントに適切。

**セットアップ：**

1. S3バケットを作成
2. 静的ウェブサイトホスティングを有効化
3. `build/`のコンテンツをアップロード
4. CloudFrontディストリビューションを作成
5. カスタムドメインを設定
6. SSL証明書を設定

**AWSの考慮事項：**

- より複雑なセットアップ
- 使用量に応じた支払い（通常非常に安価）
- より多くの設定オプション
- エンタープライズデプロイメントに適切

#### オプション5：セルフホスト

自分のサーバーでホスト。

**Nginxを使用：**

```nginx
server {
    listen 80;
    server_name your-domain.com;
    root /var/www/ministry-mapper/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # gzip圧縮を有効化
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

    # 静的アセットをキャッシュ
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

**Apacheを使用：**

```apache
<VirtualHost *:80>
    ServerName your-domain.com
    DocumentRoot /var/www/ministry-mapper/build

    <Directory /var/www/ministry-mapper/build>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        # React Routerを有効化
        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.html$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
</VirtualHost>
```

**セルフホスティングの重要事項：**

- HTTPS経由で配信（PWA機能に必要）
- バックエンドが別のドメインにある場合は適切なCORSヘッダーを設定
- 静的アセットに適切なキャッシュヘッダーを設定
- SPAルーティングが機能することを確認（すべてのルートがindex.htmlを配信）

### デプロイメントでの環境変数

**重要**：Viteはビルド時に環境変数を注入するため、環境変数はランタイムではなくビルド時に設定する必要があります。

**Vercel/Netlify/Cloudflareの場合：**

- プラットフォームのダッシュボードで環境変数を追加
- ビルドプロセス中に利用可能になります

**セルフホストの場合：**

- `npm run build`を実行する前に環境変数を設定
- または設定を含む`.env.production`ファイルを作成

## Sentryのセットアップ（オプションですが推奨）

SentryはJavaScriptエラーとパフォーマンスを監視します。

### 1. Sentryアカウントを作成

1. [sentry.io](https://sentry.io)にアクセス
2. サインアップ（寛大な無料枠あり）
3. 新しいプロジェクトを作成
4. プラットフォームを選択：「React」

### 2. DSNを取得

1. プロジェクト作成後、DSNが表示されます
2. またはSettings → Projects → [Your Project] → Client Keys (DSN)に移動
3. DSN URLをコピー

### 3. 環境に追加

```bash
VITE_SENTRY_DSN=https://your_key@your_org.sentry.io/your_project_id
```

**注意**：Sentryは`VITE_SYSTEM_ENVIRONMENT`が`production`に設定されている場合のみアクティブです。

### 4. Sentryを設定（オプション）

**ソースマップ**：ビルドプロセスでソースマップが自動的に生成され、縮小された本番コードのデバッグに役立ちます。

**アラート**：Sentryダッシュボードで設定：

- 新しい問題のメール通知
- Slack/Discord統合
- 重大なエラーのカスタムアラートルール
- リリース追跡

## 多言語サポート

Ministry Mapperには7つの言語がすぐに使える状態で含まれています。

### 対応言語

- 英語（`en`）
- スペイン語（`es` - Español）
- 日本語（`ja` - 日本語）
- 韓国語（`ko` - 한국어）
- 中国語（`zh` - 中文）
- インドネシア語（`id` - Bahasa Indonesia）
- マレー語（`ms` - Bahasa Melayu）

### 言語検出の仕組み

- `i18next-browser-languagedetector`を使用
- 初回訪問時にブラウザ設定から自動検出
- ユーザーはナビゲーションバーの言語セレクターで手動で言語を切り替え可能
- 選択は`localStorage`に保存され、ブラウザ設定よりも優先
- 選択した言語がサポートされていない場合は英語にフォールバック

### 新しい言語の追加

1. **翻訳ファイルを作成**

   - `src/i18n/locales/`に移動
   - 新しいファイルを作成：`[language-code].json`（例：フランス語の場合`fr.json`）
   - `en.json`から構造をコピー

2. **すべての文字列を翻訳**

   - すべてのキーと値のペアを翻訳
   - 同じJSON構造を維持
   - `{{variable}}`のようなプレースホルダー変数を維持

3. **言語を登録**
   - `src/i18n/index.ts`を編集
   - 翻訳ファイルをインポート
   - resourcesオブジェクトに追加

フランス語の例：

```typescript
import fr from './locales/fr.json';

resources: {
  en: { translation: en },
  fr: { translation: fr },
  // ... 他の言語
}
```

## プログレッシブウェブアプリ（PWA）

Ministry MapperにはVite PWAプラグインによるPWAサポートが含まれています。

### PWA機能

- **インストール可能**：モバイル/デスクトップのホーム画面にインストール可能
- **オフラインアセット**：静的ファイルがキャッシュされ、高速な読み込みが可能
- **サービスワーカー**：自動的に生成され、登録
- **キャッシュ戦略**：外部アセットにはStaleWhileRevalidate、フォントにはCacheFirst

### PWA設定

`vite.config.js`での設定：

```javascript
VitePWA({
  registerType: "autoUpdate", // サービスワーカーを自動更新
  manifest: false, // マニフェストなし（必要に応じて追加可能）
  workbox: {
    globPatterns: ["**/*.{js,css,html,ico,png,svg,woff2}"],
    skipWaiting: true, // 新しいサービスワーカーを即座にアクティブ化
    clientsClaim: true, // クライアントを即座に制御
    cleanupOutdatedCaches: true, // 古いキャッシュを削除
  },
});
```

### キャッシュ戦略

**外部アセット**（例：assets.ministry-mapper.comから）：

- 戦略：StaleWhileRevalidate
- キャッシュ名：「external-assets」
- 有効期限：7日、最大50エントリ

**フォント**：

- 戦略：CacheFirst
- キャッシュ名：「fonts」
- 有効期限：30日、最大30エントリ

**注意**：アプリはデータ操作（PocketBase接続）にインターネットが必要です。静的アセットのみがオフラインでキャッシュされます。

## デプロイメントのテスト

### 機能チェックリスト

- [ ] ホームページが正しく読み込まれる
- [ ] サインアップページが機能する
- [ ] メール確認が機能する
- [ ] ログインページが機能する
- [ ] OTP認証が機能する（有効な場合）
- [ ] パスワードリセットが機能する
- [ ] 区域セレクターが表示される（司会者/管理者の場合）
- [ ] 地図が正しく読み込まれ、表示される
- [ ] 区域を表示できる
- [ ] 住所ステータスを更新できる
- [ ] 割り当てリンクが機能する
- [ ] リンクが正しく期限切れになる
- [ ] モバイルビューがレスポンシブ
- [ ] 言語検出が機能する
- [ ] すべてのモーダルが正しく開閉する

### パフォーマンスチェック

- [ ] 初期ページ読み込みが3秒未満
- [ ] Lighthouseスコア > 90
- [ ] コンソールエラーなし
- [ ] 画像が迅速に読み込まれる
- [ ] 地図がレスポンシブ
- [ ] スムーズなスクロールとナビゲーション

### セキュリティチェック

- [ ] HTTPSが強制されている（HTTPアクセスなし）
- [ ] プライバシーポリシーリンクが機能する
- [ ] 利用規約リンクが機能する
- [ ] APIキーがブラウザコードに露出していない
- [ ] バックエンドのCORSが正しく設定されている
- [ ] PocketBase接続が安全

### ブラウザテスト

複数のブラウザでテスト：

- [ ] Chrome/Chromium
- [ ] Firefox
- [ ] Safari（macOS/iOS）
- [ ] Edge
- [ ] モバイルブラウザ

## トラブルシューティング

### バックエンド接続失敗

**問題**：「サーバーに接続できません」または「ネットワークエラー」

**解決策：**

- `VITE_POCKETBASE_URL`が正しいことを確認（末尾のスラッシュなし）
- PocketBaseバックエンドが実行中か確認
- ブラウザでバックエンドURLを直接テスト
- PocketBaseのCORS設定を確認（フロントエンドドメインを許可する必要がある）
- ブラウザDevTools → Networkタブを開いて正確なエラーを確認
- デプロイメント場所からバックエンドがアクセス可能か確認

### ビルドエラー

**問題**：`npm run build`が失敗

**解決策：**

- すべての環境変数が正しく設定されていることを確認
- `npm install`を実行して依存関係を更新
- `node_modules`を削除して再インストール：`rm -rf node_modules && npm install`
- Node.jsバージョンを確認：`node --version`（24以上が必要）
- TypeScriptエラーを確認：具体的なエラーメッセージを確認
- `npm run prettier:fix`を試してフォーマットの問題を修正
- Viteキャッシュをクリア：`rm -rf node_modules/.vite`

### TypeScriptエラー

**問題**：ビルド中の型エラー

**解決策：**

- 型定義について`src/utils/interface.ts`を確認
- インポートが正しいことを確認
- `npx tsc --noEmit`を実行してすべての型エラーを確認
- すべての依存関係がインストールされていることを確認

### パフォーマンスの低下

**問題**：アプリが遅いまたは遅延がある

**解決策：**

- インターネット接続速度を確認
- ブラウザキャッシュをクリアしてリロード
- PocketBaseバックエンドが迅速に応答していることを確認
- ブラウザコンソールでJavaScriptエラーを確認
- 別のデバイス/ブラウザでテストして問題を特定
- Lighthouseパフォーマンススコアを確認
- サービスワーカーが機能していることを確認：DevTools → Application → Service Workers

### PWAインストールの問題

**問題**：「ホーム画面に追加」が表示されない

**解決策：**

- HTTPS経由で配信されている必要がある
- DevToolsでサービスワーカーの登録を確認
- PWA要件が満たされていることを確認（マニフェスト、サービスワーカーなど）
- 別のブラウザを試す（Safari、ChromeはPWAサポートが異なる）

### リアルタイム更新が機能しない

**問題**：他のユーザーの変更が表示されない

**解決策：**

- PocketBaseリアルタイムサブスクリプションが機能していることを確認
- ブラウザコンソールでSSE（Server-Sent Events）エラーを確認
- バックエンドSSEエンドポイントがアクセス可能か確認
- 複数のユーザーが実際に同じ区域を表示しているか確認
- ページを更新して再接続を強制

## メンテナンス

### 依存関係を最新に保つ

```bash
# リポジトリから最新の変更をプル
git pull origin main

# 更新された依存関係をインストール
npm install

# セキュリティ脆弱性を確認
npm audit

# セキュリティ問題を自動修正（可能な場合）
npm audit fix

# 古いパッケージを確認
npm outdated

# 特定のパッケージを更新
npm update package-name
```

### 監視

**Sentryダッシュボード：**

- 毎週新しいエラーを確認
- エラートレンドをレビュー
- 重大な問題を迅速に修正
- 優先度の高いエラーのアラートを設定

**Google Cloud Console：**

- クォータ内に収まるようにAPI使用量を監視
- 使用量の異常なスパイクを確認
- 請求アラートを確認
- APIが有効なままであることを確認

**パフォーマンス：**

- 定期的にLighthouse監査を実行
- ページ読み込み時間を監視
- Core Web Vitalsを確認
- 遅い接続でテスト

**ユーザーフィードバック：**

- 会衆メンバーの声を聞く
- 一般的な問題を追跡
- 解決策を文書化
- 必要に応じてドキュメントを更新

### バージョン更新

Ministry Mapperはセマンティックバージョニングを使用しています：

- **メジャーバージョン**（x.0.0）：破壊的変更
- **マイナーバージョン**（1.x.0）：新機能
- **パッチバージョン**（1.9.x）：バグ修正

## 次のステップ

**ユーザー向け：**

- アプリケーションの使用方法については[ユーザーガイド](user-guide.md)をお読みください

**管理者向け：**

- PocketBaseバックエンドをセットアップ（ministry-mapper-beリポジトリを参照）
- 会衆設定を構成
- ユーザーを招待してロールを割り当て
- 区域を作成して住所を追加
