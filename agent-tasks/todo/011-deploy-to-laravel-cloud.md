# タスク011: Laravel Cloudへのデプロイ

## 目的
Laravel Cloudに実際にデプロイし、本番環境で動作確認

## 作業内容

### 1. Laravel Cloudでプロジェクト作成
1. Laravel Cloudダッシュボードにログイン
2. "New Project"をクリック
3. GitHubリポジトリを選択（mu-total）
4. プロジェクト名を設定
5. リージョンを選択（Tokyo推奨）
6. プランを選択

### 2. 環境変数の設定
Laravel Cloudの環境変数設定画面で以下を設定：

```env
APP_NAME=mu-total
APP_ENV=production
APP_DEBUG=false
APP_KEY=<自動生成されたキー>
APP_URL=<Laravel Cloudが提供するURL>

DB_CONNECTION=mysql
DB_HOST=<Laravel Cloudが提供>
DB_PORT=3306
DB_DATABASE=<Laravel Cloudが提供>
DB_USERNAME=<Laravel Cloudが提供>
DB_PASSWORD=<Laravel Cloudが提供>

SESSION_DRIVER=database
QUEUE_CONNECTION=database

LOG_CHANNEL=stack
LOG_LEVEL=error
```

### 3. データベースの設定
- Laravel Cloudが提供するMySQLデータベースを使用
- 接続情報は自動的に環境変数に設定される

### 4. 初回デプロイ
1. "Deploy"ボタンをクリック
2. デプロイログを確認
3. エラーがないか確認

**デプロイプロセス:**
- コードのクローン
- 依存関係のインストール（composer install）
- フロントエンドビルド（npm install & npm run build）
- キャッシュ生成
- マイグレーション実行
- アプリケーションの起動

### 5. マイグレーションとシーダーの実行
Laravel Cloudのコンソールまたはデプロイ設定で：
```bash
php artisan migrate --force
php artisan db:seed --force
```

### 6. ストレージリンクの作成
```bash
php artisan storage:link
```

### 7. 動作確認

**基本動作**
- [ ] トップページが表示される
- [ ] グループ作成ができる
- [ ] グループページが表示される
- [ ] 子どもを追加できる
- [ ] ストック管理ページが表示される
- [ ] ストックの増減ができる

**セキュリティ確認**
- [ ] HTTPSでアクセスできる
- [ ] CSRFトークンが機能している
- [ ] 不正なアクセスが拒否される

**パフォーマンス確認**
- [ ] ページ読み込みが速い（3秒以内）
- [ ] アセットが圧縮されている
- [ ] キャッシュが効いている

### 8. エラーログの確認
Laravel Cloudのログ画面で：
- エラーログがないか確認
- 警告ログがないか確認

### 9. カスタムドメインの設定（オプション）
1. カスタムドメインを追加
2. DNSレコードを設定
3. SSL証明書の自動取得を確認

### 10. モニタリング設定
- Laravel Cloudのモニタリング機能を有効化
- アラート設定（エラー発生時など）

### 11. バックアップ設定
- データベースの自動バックアップを有効化
- バックアップ頻度の設定（日次推奨）

### 12. デプロイ自動化の設定
- mainブランチへのpush時に自動デプロイを有効化
- または手動デプロイのみにする

## トラブルシューティング

### デプロイが失敗する場合
1. ログを確認
2. ローカルで`composer install --no-dev`が成功するか確認
3. `npm run build`が成功するか確認
4. 環境変数が正しく設定されているか確認

### マイグレーションが失敗する場合
1. データベース接続情報を確認
2. マイグレーションファイルに構文エラーがないか確認
3. ローカルで`php artisan migrate:fresh`が成功するか確認

### ページが表示されない場合
1. APP_KEYが設定されているか確認
2. ルーティングが正しいか確認
3. ビューファイルが存在するか確認
4. Laravel Cloudのログを確認

### アセットが読み込まれない場合
1. `npm run build`が成功しているか確認
2. `public/build`ディレクトリが生成されているか確認
3. vite.config.jsの設定を確認

## 完了条件
- [ ] Laravel Cloudプロジェクト作成完了
- [ ] 環境変数設定完了
- [ ] 初回デプロイ成功
- [ ] マイグレーション・シーダー実行完了
- [ ] 全機能の動作確認完了
- [ ] セキュリティ確認完了
- [ ] パフォーマンス確認完了
- [ ] モニタリング設定完了
- [ ] バックアップ設定完了

## 備考
- デプロイ後は必ず動作確認を行う
- 問題があれば即座にロールバック
- 本番環境のデータは慎重に扱う
- 定期的にバックアップを確認
