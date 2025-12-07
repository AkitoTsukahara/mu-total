# タスク010: Laravel Cloud デプロイ準備

## 目的
Laravel Cloudへのデプロイに必要な設定を行う

## 作業内容

### 1. Laravel Cloudアカウント作成
- https://cloud.laravel.com/ にアクセス
- アカウント登録
- GitHubアカウントと連携

### 2. プロジェクトの準備

**composer.jsonの確認**
- PHP 8.4以上を指定
- 必要な拡張機能を確認

**package.jsonの確認**
- ビルドスクリプトが正しく設定されているか確認

### 3. 環境変数の整理

**.env.example の更新**
Laravel Cloud用の環境変数を追加：
```env
APP_NAME=mu-total
APP_ENV=production
APP_DEBUG=false
APP_URL=https://your-app.laravel.cloud

DB_CONNECTION=mysql
DB_HOST=
DB_PORT=3306
DB_DATABASE=
DB_USERNAME=
DB_PASSWORD=

SESSION_DRIVER=database
QUEUE_CONNECTION=database

# その他必要な環境変数
```

### 4. デプロイ設定ファイルの作成

**laravel-cloud.yml（存在する場合）の確認**
```yaml
# ビルドコマンド
build:
  - composer install --no-dev --optimize-autoloader
  - npm install
  - npm run build
  - php artisan config:cache
  - php artisan route:cache
  - php artisan view:cache

# デプロイ後のコマンド
deploy:
  - php artisan migrate --force
```

### 5. データベース設定
- Laravel Cloudで提供されるMySQLデータベースを使用
- 環境変数で接続情報を設定

### 6. ストレージ設定
- `storage/app/public` のシンボリックリンク設定
- アップロードファイルの保存先確認

### 7. セキュリティ設定

**CSRF保護の確認**
```php
// app/Http/Middleware/VerifyCsrfToken.php
protected $except = [
    // 必要に応じて例外を設定
];
```

**HTTPS強制**
```php
// app/Providers/AppServiceProvider.php
public function boot()
{
    if ($this->app->environment('production')) {
        \URL::forceScheme('https');
    }
}
```

### 8. エラーハンドリング
- 本番環境用のエラーページ作成
- `resources/views/errors/404.blade.php`
- `resources/views/errors/500.blade.php`

### 9. パフォーマンス最適化

**キャッシュ設定**
```bash
# デプロイ時に実行されるコマンド
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

**アセット最適化**
```bash
npm run build
```

### 10. GitHub連携
- リポジトリがGitHubにプッシュされていることを確認
- mainブランチが最新であることを確認

### 11. デプロイ前チェックリスト
- [ ] .env.exampleが最新
- [ ] composer.json/package.jsonが正しい
- [ ] ビルドスクリプトが動作する
- [ ] マイグレーションファイルが揃っている
- [ ] シーダーが正しく動作する
- [ ] エラーページが用意されている
- [ ] HTTPS強制設定が入っている
- [ ] GitHubリポジトリが最新

### 12. ローカルで本番ビルドテスト
```bash
# 本番環境を想定したビルド
composer install --no-dev --optimize-autoloader
npm run build
php artisan config:cache
php artisan route:cache
php artisan view:cache

# 動作確認
php artisan serve
```

問題なければリセット：
```bash
php artisan config:clear
php artisan route:clear
php artisan view:clear
composer install
```

## 完了条件
- [ ] Laravel Cloudアカウント作成完了
- [ ] 環境変数整理完了
- [ ] デプロイ設定完了
- [ ] セキュリティ設定完了
- [ ] エラーページ作成完了
- [ ] パフォーマンス最適化完了
- [ ] ローカルで本番ビルドテスト成功
- [ ] GitHubリポジトリ最新化完了

## 備考
- Laravel Cloudの公式ドキュメントを参照
- デプロイ前に必ずテスト環境で確認
