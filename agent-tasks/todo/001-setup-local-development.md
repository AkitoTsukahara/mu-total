# タスク001: ローカル開発環境のセットアップ

## 目的
Laravelプロジェクトをローカルで動作させる

## 作業内容

### 1. 依存関係のインストール
```bash
# PHP依存関係
composer install

# JavaScript依存関係
npm install
```

### 2. 環境変数の設定
```bash
# .envファイル作成
cp .env.example .env

# アプリケーションキー生成
php artisan key:generate
```

### 3. .envファイルの編集
以下の項目を設定：
- `APP_NAME=mu-total`
- `APP_ENV=local`
- `APP_DEBUG=true`
- `DB_CONNECTION=mysql`
- `DB_HOST=127.0.0.1`
- `DB_PORT=3306`
- `DB_DATABASE=mu_total`
- `DB_USERNAME=<your_username>`
- `DB_PASSWORD=<your_password>`

### 4. データベースのセットアップ
```bash
# データベース作成（MySQLにログインして実行）
CREATE DATABASE mu_total CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

# マイグレーション実行
php artisan migrate

# シーダー実行
php artisan db:seed
```

### 5. 開発サーバー起動
```bash
# Laravelサーバー起動
php artisan serve

# 別ターミナルでフロントエンドビルド
npm run dev
```

### 6. 動作確認
- http://localhost:8000 にアクセス
- Laravelのウェルカムページが表示されることを確認

## 完了条件
- [ ] composer install成功
- [ ] npm install成功
- [ ] .env設定完了
- [ ] データベースマイグレーション成功
- [ ] 開発サーバーが起動
- [ ] ブラウザでアクセスできる

## 備考
- MySQLは事前にインストールしておく必要がある
- Node.js 22 LTS推奨
- PHP 8.4以上必須
