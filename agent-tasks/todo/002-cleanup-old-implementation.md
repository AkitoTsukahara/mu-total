# タスク002: 古い実装の整理

## 目的
SvelteKit時代の古い実装や不要なコードを整理し、Bladeベースの構成に最適化する

## 作業内容

### 1. 古いAPI実装の確認と整理
- `app/Http/Controllers/Api/` 配下のコントローラーを確認
- SvelteKit用に作られたJSON APIが残っている場合は、後でBlade用に調整するためマーク

### 2. 不要なディレクトリ・ファイルの削除
確認して削除が必要なもの：
- `resources/js/` 配下のSvelteKit用コード（あれば）
- 不要なnpmパッケージの確認（package.json）
- フロントエンド用の設定ファイル（tsconfig.json, svelte.config.js等があれば）

### 3. package.jsonの整理
```bash
# 不要な依存関係を削除
# SvelteKit関連のパッケージがあれば削除
# Blade + Alpine.js/Livewire用のパッケージに置き換え
```

### 4. vite.config.jsの確認
- Laravel標準のVite設定になっているか確認
- 不要な設定があれば削除

### 5. ルーティングの確認
- `routes/web.php` - Blade用のルート
- `routes/api.php` - API用のルート
- 古いSvelteKit用のルートがあれば整理

## 完了条件
- [ ] 不要なファイル・ディレクトリを削除
- [ ] package.jsonを整理
- [ ] vite.config.jsを確認・修正
- [ ] ルーティングを整理
- [ ] コードが正常に動作することを確認

## 備考
- 削除する前にgit statusで確認
- 重要なファイルは誤って削除しないよう注意
