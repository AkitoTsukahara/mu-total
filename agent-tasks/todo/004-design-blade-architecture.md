# タスク004: Bladeベースのフロントエンド設計

## 目的
SvelteKitから移行するBladeベースのフロントエンドアーキテクチャを設計

## 作業内容

### 1. 技術スタック選定
以下のいずれかを選択：

**オプションA: Blade + Alpine.js + Tailwind CSS**
- メリット: シンプル、学習コスト低い、Laravel標準
- デメリット: 複雑なインタラクションには向かない

**オプションB: Blade + Livewire + Tailwind CSS**
- メリット: リアクティブ、SPA風、Laravel公式
- デメリット: サーバー負荷が若干高い

**オプションC: Blade + Inertia.js + Vue/React**
- メリット: SPA、豊富なエコシステム
- デメリット: セットアップが複雑

**推奨: オプションA or B**（シンプルさ重視）

### 2. ディレクトリ構成設計
```
resources/
├── views/
│   ├── layouts/
│   │   ├── app.blade.php        # メインレイアウト
│   │   └── guest.blade.php      # ゲスト用レイアウト
│   ├── components/
│   │   ├── button.blade.php     # 再利用可能なコンポーネント
│   │   ├── stock-item.blade.php
│   │   └── child-card.blade.php
│   ├── pages/
│   │   ├── welcome.blade.php    # トップページ
│   │   ├── group/
│   │   │   ├── create.blade.php
│   │   │   └── show.blade.php
│   │   └── children/
│   │       ├── index.blade.php
│   │       └── stock.blade.php
│   └── partials/
│       ├── header.blade.php
│       └── footer.blade.php
├── css/
│   └── app.css                   # Tailwind CSS
└── js/
    └── app.js                    # Alpine.js/エントリーポイント
```

### 3. ルーティング設計
```php
// routes/web.php
Route::get('/', HomeController::class)->name('home');

Route::prefix('group')->group(function () {
    Route::get('/create', [GroupController::class, 'create'])->name('group.create');
    Route::post('/', [GroupController::class, 'store'])->name('group.store');
    Route::get('/{token}', [GroupController::class, 'show'])->name('group.show');
});

Route::prefix('group/{token}/children')->group(function () {
    Route::get('/', [ChildrenController::class, 'index'])->name('children.index');
    Route::post('/', [ChildrenController::class, 'store'])->name('children.store');
    Route::get('/{child}/stock', [StockController::class, 'show'])->name('stock.show');
    Route::post('/{child}/stock/increment', [StockController::class, 'increment'])->name('stock.increment');
    Route::post('/{child}/stock/decrement', [StockController::class, 'decrement'])->name('stock.decrement');
});
```

### 4. 既存のSvelteKitページとの対応表作成
| SvelteKitルート | Bladeルート | 説明 |
|----------------|-------------|------|
| `/` | `/` | トップページ（グループ作成） |
| `/group/[token]` | `/group/{token}` | グループページ（子ども一覧） |
| `/group/[token]/child/[childId]` | `/group/{token}/children/{child}/stock` | ストック管理ページ |

### 5. 状態管理方針
- セッションベースの認証（トークンベース）
- フォーム送信はCSRF保護
- リアルタイム更新が必要な場合はAlpine.js/Livewireで対応

### 6. スタイリング方針
- Tailwind CSS使用
- レスポンシブデザイン（モバイルファースト）
- ダークモード対応は後回し

## 完了条件
- [ ] 技術スタック決定
- [ ] ディレクトリ構成設計完了
- [ ] ルーティング設計完了
- [ ] SvelteKitとの対応表作成
- [ ] 状態管理方針決定
- [ ] スタイリング方針決定

## 備考
- 設計ドキュメントとしてagent-context/blade-architecture.mdを作成
- チーム（またはAI）と合意を取る
