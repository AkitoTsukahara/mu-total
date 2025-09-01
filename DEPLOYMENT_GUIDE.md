# AWS LightSail デプロイメントガイド

## 前提条件
- AWS LightSailインスタンス（Ubuntu 22.04 LTS、最小1GB RAM）
- GitHubリポジトリにプロジェクトがプッシュ済み
- LightSailのポート80、443が開放済み

## デプロイ手順

### 1. LightSailインスタンスの初期設定

SSHでLightSailインスタンスに接続後、以下を実行：

```bash
# システムを最新状態に更新
sudo apt update && sudo apt upgrade -y

# 必要なパッケージをインストール
sudo apt install -y curl git wget unzip software-properties-common

# Docker と Docker Compose をインストール
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Dockerサービスを開始・有効化
sudo systemctl start docker
sudo systemctl enable docker

# 現在のユーザーをdockerグループに追加
sudo usermod -aG docker $USER

# スワップファイルを作成（メモリ不足対策）
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# 再ログインして変更を適用
exit
```

再度SSHで接続します。

### 2. プロジェクトのクローンと設定

```bash
# プロジェクトディレクトリを作成
sudo mkdir -p /var/www
sudo chown -R $USER:$USER /var/www
cd /var/www

# GitHubからプロジェクトをクローン
git clone https://github.com/YOUR_USERNAME/fuku-pochi.git
cd fuku-pochi

# LightSailインスタンスのIPアドレスを取得
export SERVER_IP=$(curl -s http://169.254.169.254/latest/meta-data/public-ipv4)
echo "Server IP: $SERVER_IP"
```

### 3. 環境変数ファイルの作成

```bash
# .env.productionファイルを作成
cat > .env.production << EOF
# ==============================================================================
# FukuPochi 本番環境設定
# ==============================================================================

# アプリケーション基本設定
APP_NAME=FukuPochi
APP_ENV=production
APP_KEY=base64:YOUR_32_CHARACTER_SECRET_KEY_HERE
APP_DEBUG=false
SERVER_IP=$SERVER_IP
APP_URL=http://\${SERVER_IP}

# 言語・地域設定
APP_LOCALE=ja
APP_FALLBACK_LOCALE=en
APP_FAKER_LOCALE=ja_JP

# ログ設定
LOG_CHANNEL=stack
LOG_LEVEL=error

# データベース設定
DB_CONNECTION=mysql
DB_HOST=database
DB_PORT=3306
DB_DATABASE=fukupochi_prod
DB_USERNAME=fukupochi_user
DB_PASSWORD=$(openssl rand -base64 32)
DB_ROOT_PASSWORD=$(openssl rand -base64 32)

# セッション設定
SESSION_DRIVER=database
SESSION_LIFETIME=120
SESSION_ENCRYPT=false
SESSION_PATH=/
SESSION_DOMAIN=\${SERVER_IP}

# キャッシュ・キュー設定
BROADCAST_CONNECTION=log
FILESYSTEM_DISK=local
QUEUE_CONNECTION=database
CACHE_STORE=redis
CACHE_DRIVER=redis

# Redis設定
REDIS_CLIENT=phpredis
REDIS_HOST=redis
REDIS_PASSWORD=$(openssl rand -base64 32)
REDIS_PORT=6379

# メール設定
MAIL_MAILER=log
MAIL_FROM_ADDRESS="noreply@\${SERVER_IP}"
MAIL_FROM_NAME="\${APP_NAME}"

# CORS・API設定
FRONTEND_URL=http://\${SERVER_IP}
SANCTUM_STATEFUL_DOMAINS=\${SERVER_IP}

# タイムゾーン
TZ=Asia/Tokyo
EOF

# 生成されたパスワードを確認（必要に応じて保存）
echo "Generated passwords saved in .env.production"
```

### 4. Laravel APP_KEYの生成

```bash
# 一時的にコンテナを起動してAPP_KEYを生成
docker run --rm -v $(pwd)/backend:/app -w /app composer:latest composer install --no-dev --optimize-autoloader
APP_KEY=$(docker run --rm -v $(pwd)/backend:/app -w /app php:8.4-cli php artisan key:generate --show)

# .env.productionのAPP_KEYを更新
sed -i "s|APP_KEY=.*|APP_KEY=$APP_KEY|" .env.production
echo "APP_KEY generated and updated"
```

### 5. Nginxの設定ファイルを作成

```bash
# IPアドレス用のNginx設定を作成
cat > nginx/nginx.ip.conf << 'EOF'
user nginx;
worker_processes auto;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;
    error_log /var/log/nginx/error.log warn;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    client_max_body_size 20M;
    client_body_buffer_size 128k;

    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/javascript application/xml+rss application/json;

    upstream frontend {
        server frontend:3000;
    }

    upstream backend {
        server backend:9000;
    }

    server {
        listen 80;
        server_name _;

        root /var/www/html/public;
        index index.php index.html;

        # Health check endpoint
        location /health {
            access_log off;
            return 200 "healthy\n";
            add_header Content-Type text/plain;
        }

        # Frontend (SvelteKit)
        location / {
            proxy_pass http://frontend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_cache_bypass $http_upgrade;
            proxy_read_timeout 86400;
        }

        # Backend API (Laravel)
        location /api {
            try_files $uri $uri/ /index.php?$query_string;
        }

        # Laravel storage files
        location /storage {
            alias /var/www/html/storage/app/public;
            expires 30d;
            add_header Cache-Control "public, immutable";
        }

        # PHP processing
        location ~ \.php$ {
            fastcgi_pass backend;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
            fastcgi_buffer_size 128k;
            fastcgi_buffers 4 256k;
            fastcgi_busy_buffers_size 256k;
        }

        # Deny access to hidden files
        location ~ /\. {
            deny all;
            access_log off;
            log_not_found off;
        }
    }
}
EOF
```

### 6. デプロイの実行

```bash
# Dockerイメージをビルド
docker compose -f docker-compose.production.yml build

# コンテナを起動
docker compose -f docker-compose.production.yml up -d

# 起動状態を確認
docker compose -f docker-compose.production.yml ps

# ログを確認（問題がある場合）
docker compose -f docker-compose.production.yml logs -f
```

### 7. Laravel初期設定

```bash
# データベースマイグレーション
docker compose -f docker-compose.production.yml exec backend php artisan migrate --force

# ストレージリンクを作成
docker compose -f docker-compose.production.yml exec backend php artisan storage:link

# Laravelの最適化
docker compose -f docker-compose.production.yml exec backend php artisan config:cache
docker compose -f docker-compose.production.yml exec backend php artisan route:cache
docker compose -f docker-compose.production.yml exec backend php artisan view:cache

# パーミッション設定
docker compose -f docker-compose.production.yml exec backend chmod -R 777 storage bootstrap/cache
```

### 8. 動作確認

```bash
# サービスの状態確認
curl -I http://$SERVER_IP/health
curl -I http://$SERVER_IP/api/health

# ブラウザでアクセス
echo "Application URL: http://$SERVER_IP"
```

## メンテナンス作業

### アプリケーションの更新

```bash
cd /var/www/fuku-pochi

# 最新コードを取得
git pull origin main

# コンテナを再ビルド・再起動
docker compose -f docker-compose.production.yml down
docker compose -f docker-compose.production.yml build --no-cache
docker compose -f docker-compose.production.yml up -d

# マイグレーション実行
docker compose -f docker-compose.production.yml exec backend php artisan migrate --force

# キャッシュクリア
docker compose -f docker-compose.production.yml exec backend php artisan cache:clear
docker compose -f docker-compose.production.yml exec backend php artisan config:cache
docker compose -f docker-compose.production.yml exec backend php artisan route:cache
docker compose -f docker-compose.production.yml exec backend php artisan view:cache
```

### ログ確認

```bash
# 全サービスのログ
docker compose -f docker-compose.production.yml logs -f

# 特定サービスのログ
docker compose -f docker-compose.production.yml logs -f nginx
docker compose -f docker-compose.production.yml logs -f backend
docker compose -f docker-compose.production.yml logs -f frontend
docker compose -f docker-compose.production.yml logs -f database
```

### バックアップ

```bash
# データベースバックアップ
docker compose -f docker-compose.production.yml exec database mysqldump -u root -p${DB_ROOT_PASSWORD} fukupochi_prod > backup_$(date +%Y%m%d_%H%M%S).sql

# アップロードファイルのバックアップ
tar czf storage_backup_$(date +%Y%m%d_%H%M%S).tar.gz -C /var/www/fuku-pochi/backend/storage app/public
```

### トラブルシューティング

#### メモリ不足の場合
```bash
# メモリ使用状況確認
free -h
docker stats

# 不要なDockerリソースを削除
docker system prune -a --volumes
```

#### コンテナが起動しない場合
```bash
# 詳細なエラーログを確認
docker compose -f docker-compose.production.yml logs --tail=50

# コンテナを個別に起動してデバッグ
docker compose -f docker-compose.production.yml up database
docker compose -f docker-compose.production.yml up redis
docker compose -f docker-compose.production.yml up backend
docker compose -f docker-compose.production.yml up frontend
docker compose -f docker-compose.production.yml up nginx
```

#### ポート競合の場合
```bash
# 使用中のポートを確認
sudo netstat -tulpn | grep :80
sudo lsof -i :80

# 必要に応じてプロセスを停止
sudo systemctl stop apache2  # Apacheが動いている場合
```

## セキュリティ設定（推奨）

### ファイアウォール設定
```bash
# UFWを有効化
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw --force enable
sudo ufw status
```

### 自動アップデート設定
```bash
# unattended-upgradesをインストール
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

## 監視設定（オプション）

### ヘルスチェックスクリプト
```bash
# /var/www/fuku-pochi/scripts/health-check.sh を作成
cat > /var/www/fuku-pochi/scripts/health-check.sh << 'EOF'
#!/bin/bash
HEALTH_URL="http://localhost/health"
RESPONSE=$(curl -s -o /dev/null -w "%{http_code}" $HEALTH_URL)

if [ $RESPONSE -ne 200 ]; then
    echo "Health check failed with status $RESPONSE"
    docker compose -f /var/www/fuku-pochi/docker-compose.production.yml restart
fi
EOF

chmod +x /var/www/fuku-pochi/scripts/health-check.sh

# cronに追加（5分ごとにチェック）
(crontab -l 2>/dev/null; echo "*/5 * * * * /var/www/fuku-pochi/scripts/health-check.sh") | crontab -
```

## 本番環境への移行チェックリスト

- [ ] LightSailインスタンスが起動している
- [ ] Docker/Docker Composeがインストール済み
- [ ] GitHubからコードをクローン済み
- [ ] .env.productionファイルを設定済み
- [ ] APP_KEYを生成・設定済み
- [ ] すべてのコンテナが正常に起動している
- [ ] データベースマイグレーションが完了
- [ ] ブラウザからアクセス可能
- [ ] ログにエラーが出ていない
- [ ] バックアップ手順を確認済み