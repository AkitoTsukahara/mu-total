# タスク003: ドキュメントの更新

## 目的
古い情報（SvelteKit、Docker、Laravel Vapor等）を最新の構成に合わせて更新

## 作業内容

### 1. README.mdの更新
以下の点を修正：
- プロジェクト名: fuku-pochi → mu-total
- 技術スタック: SvelteKit削除、Blade追加
- デプロイ先: Laravel Vapor → Laravel Cloud
- 開発環境: Docker削除、ローカル環境の手順に変更
- クイックスタートガイドを最新化

### 2. agent-context/architecture.mdの更新
以下の点を修正：
- フロントエンド: SvelteKit → Blade + Alpine.js/Livewire
- デプロイ候補: Laravel Vapor → Laravel Cloud
- ディレクトリ構成: backend/ → ルート直下
- モノリス構成であることを明記

### 3. agent-context/api-spec.mdの更新
以下の点を修正：
- BladeからのAPI呼び出しを想定した説明に変更
- CSRFトークンの扱いについて追記
- セッション認証について追記（必要に応じて）

### 4. agent-context/dev-env.mdの更新
以下の点を修正：
- Docker関連の記述を削除
- ローカル開発環境のセットアップ手順に変更
- 必要なソフトウェア（PHP, MySQL, Node.js）の記載

### 5. agent-context/ci-cd.mdの更新
以下の点を修正：
- GitHub Actionsのワークフロー説明を最新化
- Laravel Cloudへのデプロイ設定について追記

### 6. agent-context/design-doc.mdの確認
- UI/UX設計は基本的に同じだが、Blade実装に変わることを明記

## 完了条件
- [ ] README.md更新完了
- [ ] architecture.md更新完了
- [ ] api-spec.md更新完了
- [ ] dev-env.md更新完了
- [ ] ci-cd.md更新完了
- [ ] design-doc.md確認・更新完了
- [ ] すべてのドキュメントの整合性を確認

## 備考
- 一度にすべて更新せず、各ドキュメントごとに確認しながら進める
- 古い情報と新しい情報を明確に区別する
