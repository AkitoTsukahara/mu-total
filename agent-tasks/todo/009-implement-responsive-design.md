# タスク009: レスポンシブデザインの実装

## 目的
モバイルファーストで使いやすいUIを実装し、PC・タブレット・スマホで最適表示

## 作業内容

### 1. ブレークポイント設計
Tailwind CSSのデフォルトブレークポイントを使用：
- `sm`: 640px（スマホ横向き）
- `md`: 768px（タブレット）
- `lg`: 1024px（デスクトップ）
- `xl`: 1280px（大画面）

### 2. レイアウトの調整

**モバイル（デフォルト）**
- 1カラムレイアウト
- フルスクリーン
- 大きめのタップターゲット（最低44x44px）
- シンプルなナビゲーション

**タブレット（md以上）**
- 2カラムグリッド（ストック表示）
- 余白を追加
- サイドバー表示（オプション）

**デスクトップ（lg以上）**
- 3カラムグリッド（ストック表示）
- 最大幅設定（max-w-7xl等）
- より豊富な情報表示

### 3. コンポーネントのレスポンシブ対応

**resources/views/layouts/app.blade.php**
```blade
<body class="bg-gray-50">
    <div class="min-h-screen">
        <!-- ヘッダー -->
        <header class="bg-white shadow">
            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4">
                <h1 class="text-xl sm:text-2xl font-bold">mu-total</h1>
            </div>
        </header>

        <!-- メインコンテンツ -->
        <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6">
            @yield('content')
        </main>
    </div>
</body>
```

**stock-grid.blade.php**
```blade
<!-- モバイル: 2列、タブレット: 3列、デスクトップ: 4列 -->
<div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
    @foreach($stockItems as $item)
        <x-stock-item :item="$item" />
    @endforeach
</div>
```

**child-card.blade.php**
```blade
<div class="bg-white rounded-lg shadow p-4 sm:p-6">
    <h3 class="text-base sm:text-lg font-semibold">{{ $child->name }}</h3>
    <div class="mt-4 flex flex-col sm:flex-row gap-2">
        <a href="..." class="px-4 py-2 bg-blue-500 text-white rounded text-center">
            ストック管理
        </a>
        <a href="..." class="px-4 py-2 bg-gray-500 text-white rounded text-center">
            編集
        </a>
    </div>
</div>
```

### 4. フォームのレスポンシブ対応
```blade
<form class="w-full max-w-md mx-auto px-4 sm:px-0">
    <div class="mb-4">
        <label class="block text-sm sm:text-base font-medium">...</label>
        <input class="mt-1 block w-full text-base sm:text-sm rounded-md">
    </div>
    <button class="w-full sm:w-auto px-6 py-3 sm:py-2 text-base sm:text-sm">
        送信
    </button>
</form>
```

### 5. モーダル・ダイアログのレスポンシブ対応
```blade
<div class="fixed inset-0 bg-gray-600 bg-opacity-50 flex items-center justify-center px-4">
    <div class="bg-white rounded-lg p-6 w-full max-w-md">
        <!-- モーダルコンテンツ -->
    </div>
</div>
```

### 6. タッチフレンドリーなUI
- ボタンは最低44x44pxのタップエリア
- 適切な余白（padding/margin）
- スワイプ操作への対応（オプション）

### 7. フォントサイズの調整
```css
/* resources/css/app.css */
@layer base {
  html {
    font-size: 16px; /* ベースフォントサイズ */
  }

  @media (max-width: 640px) {
    html {
      font-size: 14px; /* スマホでは少し小さく */
    }
  }
}
```

### 8. テスト
各デバイスサイズでテスト：
- iPhone SE (375px)
- iPhone 12/13 (390px)
- iPad (768px)
- Desktop (1280px)

確認項目：
- [ ] レイアウト崩れがない
- [ ] テキストが読みやすい
- [ ] ボタンがタップしやすい
- [ ] フォームが使いやすい
- [ ] 横向きでも問題ない
- [ ] スクロールが自然

### 9. ビューポート設定
```blade
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=5">
```

## 完了条件
- [ ] レイアウトのレスポンシブ対応完了
- [ ] コンポーネントのレスポンシブ対応完了
- [ ] フォームのレスポンシブ対応完了
- [ ] モーダルのレスポンシブ対応完了
- [ ] タッチフレンドリーなUI実装完了
- [ ] 各デバイスサイズでテスト完了

## 備考
- Chrome DevToolsのデバイスモードでテスト
- 実機でのテストも推奨
- ユーザビリティを最優先
