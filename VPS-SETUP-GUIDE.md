# 🚀 VPS環境設定ガイド

このガイドは、クリーンアップ後のVPS環境での設定を行うためのものです。

## 📋 **必要な作業**

### 1. 環境変数ファイル作成

VPS上で以下のコマンドを実行して`.env.production`ファイルを作成してください：

```bash
cd /var/www/fuku-pochi

# テンプレートから本番用環境ファイル作成
cp .env.production.example .env.production

# エディタで編集
vi .env.production
```

### 2. 必要な環境変数設定

`.env.production`で以下の値を実際の値に変更してください：

```bash
# サーバー設定（実際のIPアドレスに変更）
SERVER_IP=54.178.217.122  # 実際のLightsail IPアドレス
APP_URL=http://54.178.217.122

# アプリケーションキー（Laravel用）
APP_KEY=base64:$(php -r "echo base64_encode(random_bytes(32));")

# データベース設定（セキュアなパスワードに変更）
DB_DATABASE=app_prod_db
DB_USERNAME=db_user_prod
DB_PASSWORD=$(openssl rand -base64 32)
DB_ROOT_PASSWORD=$(openssl rand -base64 32)

# Redis設定（セキュアなパスワードに変更）
REDIS_PASSWORD=$(openssl rand -base64 32)
```

### 3. セキュアなパスワード生成

以下のコマンドでセキュアなパスワードを生成できます：

```bash
# データベースパスワード生成
echo "DB_PASSWORD=$(openssl rand -base64 32)"
echo "DB_ROOT_PASSWORD=$(openssl rand -base64 32)"

# Redisパスワード生成
echo "REDIS_PASSWORD=$(openssl rand -base64 32)"
```

### 4. 権限設定

```bash
# .env.productionファイルの権限を制限
chmod 600 .env.production
chown deploy:deploy .env.production
```

### 5. Docker環境の起動

```bash
# 現在のコンテナ停止（存在する場合）
docker compose down

# 新しい設定で起動
docker compose up -d

# 起動状態確認
docker compose ps

# ログ確認
docker compose logs
```

### 6. アプリケーション確認

```bash
# ヘルスチェック
curl http://localhost/health
curl http://54.178.217.122/health

# ブラウザでアクセス
# http://54.178.217.122
```

## 🔧 **トラブルシューティング**

### データベース接続エラーの場合

```bash
# データベースボリューム削除（初期化）
docker volume rm fuku-pochi_mysql_data_prod -f

# 再起動
docker compose up -d database
```

### 環境変数が認識されない場合

```bash
# 環境変数を直接exportして確認
export DB_ROOT_PASSWORD=your_password
export DB_PASSWORD=your_password
export SERVER_IP=54.178.217.122

# Docker composeで起動
docker compose up -d
```

## ✅ **完了確認**

- [ ] `.env.production`ファイルが作成され、適切な値が設定されている
- [ ] セキュアなパスワードが生成・設定されている
- [ ] ファイル権限が適切に設定されている
- [ ] 全Docker サービスが正常に起動している
- [ ] Webブラウザでアプリケーションにアクセスできる

## 🚨 **重要な注意事項**

1. **パスワード管理**: 生成したパスワードは安全な場所に保管してください
2. **セキュリティ**: `.env.production`は絶対にGitにコミットしないでください
3. **バックアップ**: 設定完了後は`.env.production`をバックアップしてください
4. **ファイアウォール**: 必要に応じてUFWやセキュリティグループを設定してください

このガイドに従って設定を行えば、クリーンで安全な本番環境が構築できます。