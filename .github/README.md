# mu-total

保育園に預ける子どもの衣類ストック状況を、夫婦で共有しやすく・直感的に管理できるスマホWebアプリです。

## 技術スタック

- **バックエンド**: Laravel 12.x (PHP 8.4)
- **データベース**: MySQL 8.4 LTS
- **テスト**: PHPUnit
- **フロントエンド**: Vite + Vue/React (Laravel標準構成)

## 開発環境構築手順

### 必要な環境
- PHP 8.4以上
- Composer
- MySQL 8.4 LTS
- Node.js 22 LTS
- Git

### クイックスタート

1. **リポジトリのクローン**
```bash
git clone https://github.com/username/mu-total.git
cd mu-total
```

2. **依存関係のインストール**
```bash
# PHP依存関係
composer install

# JavaScript依存関係
npm install
```

3. **環境変数の設定**
```bash
# .envファイルの作成
cp .env.example .env

# アプリケーションキーの生成
php artisan key:generate
```

4. **.envファイルの編集**
`.env`ファイルを編集して、データベース接続情報などを設定してください：
```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=mu_total
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

5. **データベースのセットアップ**
```bash
# マイグレーション実行
php artisan migrate

# シーダー実行（必要に応じて）
php artisan db:seed
```

6. **開発サーバーの起動**
```bash
# Laravelサーバー起動
php artisan serve

# フロントエンドビルド（別ターミナル）
npm run dev
```

7. **アクセス確認**
- アプリケーション: http://localhost:8000

### 開発用コマンド

```bash
# Laravelサーバー起動
php artisan serve

# フロントエンドビルド（開発モード）
npm run dev

# フロントエンドビルド（本番モード）
npm run build

# マイグレーション実行
php artisan migrate

# マイグレーションロールバック
php artisan migrate:rollback

# データベースリフレッシュ + シーダー実行
php artisan migrate:fresh --seed

# テスト実行
php artisan test

# Tinker起動
php artisan tinker

# キャッシュクリア
php artisan cache:clear
php artisan config:clear
php artisan route:clear
php artisan view:clear
```

## プロジェクト構造

```
mu-total/
├── app/                    # アプリケーションコア
├── bootstrap/              # フレームワーク起動ファイル
├── config/                 # 設定ファイル
├── database/              # マイグレーション・シーダー
├── public/                # 公開ディレクトリ
├── resources/             # ビュー・アセット
├── routes/                # ルート定義
├── storage/               # ログ・キャッシュ
├── tests/                 # テスト
└── agent-context/         # プロジェクトドキュメント
```

## ドキュメント一覧

| ドキュメント | 目的 |
|------------|------|
| [design-doc.md](../agent-context/design-doc.md) | プロダクトのUI/UX・機能仕様 |
| [architecture.md](../agent-context/architecture.md) | 技術アーキテクチャの方針整理 |
| [api-spec.md](../agent-context/api-spec.md) | API仕様の一覧 |
| [db-schema.md](../agent-context/db-schema.md) | ER図とデータモデル設計 |
| [dev-env.md](../agent-context/dev-env.md) | ローカル開発環境構築手順 |
| [ci-cd.md](../agent-context/ci-cd.md) | CI/CD構成と運用ルール |
| [prompt-guide.md](../agent-context/prompt-guide.md) | 生成AI活用のプロンプト集 |

## ライセンス

このプロジェクトはMITライセンスの下で公開されています。
