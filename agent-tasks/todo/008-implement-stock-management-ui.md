# タスク008: ストック管理UIの実装（Blade）

## 目的
衣類ストックの表示・増減機能をBladeで実装

## 作業内容

### 1. コントローラーの作成/調整
```bash
php artisan make:controller Web/StockController
```

StockControllerに以下のメソッドを実装：
- `show($token, $childId)` - ストック一覧表示
- `increment($token, $childId)` - ストック増加処理
- `decrement($token, $childId)` - ストック減少処理

### 2. ビューの作成

**resources/views/stock/show.blade.php**
- 子ども名表示
- 戻るボタン
- 衣類カテゴリ別のストックアイテム表示（グリッド）
- 各アイテムに増減ボタン

### 3. コンポーネントの作成

**resources/views/components/stock-grid.blade.php**
```blade
<div class="grid grid-cols-2 md:grid-cols-3 gap-4">
    @foreach($stockItems as $item)
        <x-stock-item :item="$item" :token="$token" :childId="$childId" />
    @endforeach
</div>
```

**resources/views/components/stock-item.blade.php**
```blade
<div class="bg-white rounded-lg shadow p-4">
    <!-- アイコン表示 -->
    <div class="flex justify-center mb-2">
        <x-clothing-icon :category="$item->category" />
    </div>

    <!-- カテゴリ名 -->
    <h3 class="text-center font-semibold">{{ $item->category->name }}</h3>

    <!-- ストック数 -->
    <div class="text-center text-3xl font-bold my-4">
        {{ $item->stock_count }}
    </div>

    <!-- 増減ボタン -->
    <div class="flex gap-2">
        <form method="POST" action="{{ route('stock.decrement', ['token' => $token, 'child' => $childId]) }}" class="flex-1">
            @csrf
            <input type="hidden" name="category_id" value="{{ $item->category->id }}">
            <button type="submit" class="w-full px-4 py-2 bg-red-500 text-white rounded">
                -
            </button>
        </form>
        <form method="POST" action="{{ route('stock.increment', ['token' => $token, 'child' => $childId]) }}" class="flex-1">
            @csrf
            <input type="hidden" name="category_id" value="{{ $item->category->id }}">
            <button type="submit" class="w-full px-4 py-2 bg-blue-500 text-white rounded">
                +
            </button>
        </form>
    </div>
</div>
```

**resources/views/components/clothing-icon.blade.php**
- SVGアイコンの表示
- カテゴリに応じたアイコンの切り替え
```blade
@props(['category'])

@switch($category->name)
    @case('Tシャツ')
        <svg class="w-16 h-16"><!-- SVGコード --></svg>
        @break
    @case('ズボン')
        <svg class="w-16 h-16"><!-- SVGコード --></svg>
        @break
    <!-- その他のカテゴリ -->
@endswitch
```

### 4. ルーティングの設定
```php
// routes/web.php
Route::prefix('group/{token}/children/{child}')->group(function () {
    Route::get('/stock', [StockController::class, 'show'])->name('stock.show');
    Route::post('/stock/increment', [StockController::class, 'increment'])->name('stock.increment');
    Route::post('/stock/decrement', [StockController::class, 'decrement'])->name('stock.decrement');
});
```

### 5. 既存APIとの連携
- 既存の`GetStockController`, `IncrementStockController`, `DecrementStockController`を参考
- ビジネスロジックは再利用

### 6. アイコンの実装
前のfrontend/static/icons/のSVGファイルを参考に、Bladeコンポーネントとして実装：
- tshirt.svg
- pants.svg
- underwear.svg
- socks.svg
- handkerchief.svg
- hat.svg
- towel.svg
- swimwear.svg
- plastic_bag.svg

### 7. リアルタイム更新（オプション）
Alpine.jsまたはLivewireを使用して、ボタンクリック後のページ全体のリロードを避ける：
```blade
<div x-data="{ count: {{ $item->stock_count }} }">
    <div class="text-center text-3xl font-bold my-4" x-text="count"></div>
    <button @click="count++" class="...">+</button>
    <button @click="count > 0 && count--" class="...">-</button>
</div>
```

### 8. テスト
- ストック一覧が表示される
- カテゴリ別にグリッド表示される
- +ボタンでストックが増える
- -ボタンでストックが減る
- 0以下にならない
- アイコンが正しく表示される
- モバイルでも使いやすいUI

## 完了条件
- [ ] StockController実装完了
- [ ] ビュー作成完了
- [ ] stock-itemコンポーネント作成完了
- [ ] clothing-iconコンポーネント作成完了
- [ ] ルーティング設定完了
- [ ] アイコンSVG実装完了
- [ ] リアルタイム更新実装（オプション）
- [ ] 動作テスト完了

## 備考
- SVGアイコンは既存のものを流用
- レスポンシブグリッドで見やすく配置
- タップしやすいボタンサイズに設定
