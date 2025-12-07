# タスク007: 子ども管理UIの実装（Blade）

## 目的
子どもの追加・編集・削除機能をBladeで実装

## 作業内容

### 1. コントローラーの作成/調整
```bash
php artisan make:controller Web/ChildrenController
```

ChildrenControllerに以下のメソッドを実装：
- `index($token)` - 子ども一覧表示
- `create($token)` - 子ども追加フォーム表示
- `store($token)` - 子ども作成処理
- `edit($token, $childId)` - 子ども編集フォーム表示
- `update($token, $childId)` - 子ども更新処理
- `destroy($token, $childId)` - 子ども削除処理

### 2. ビューの作成

**resources/views/children/index.blade.php**
- グループ情報表示
- 子ども一覧（カード形式）
- 子ども追加ボタン
- 各子どものストック管理へのリンク

**resources/views/children/create.blade.php**
- 子ども名入力フォーム
- 戻るボタン

**resources/views/children/edit.blade.php**
- 子ども名編集フォーム
- 削除ボタン
- 戻るボタン

### 3. コンポーネントの作成

**resources/views/components/child-card.blade.php**
```blade
<div class="bg-white rounded-lg shadow p-4">
    <h3 class="text-lg font-semibold">{{ $child->name }}</h3>
    <div class="mt-4 flex gap-2">
        <a href="{{ route('stock.show', ['token' => $token, 'child' => $child->id]) }}"
           class="px-4 py-2 bg-blue-500 text-white rounded">
            ストック管理
        </a>
        <a href="{{ route('children.edit', ['token' => $token, 'child' => $child->id]) }}"
           class="px-4 py-2 bg-gray-500 text-white rounded">
            編集
        </a>
    </div>
</div>
```

**resources/views/components/child-form.blade.php**
- 子ども名入力フィールド
- 送信ボタン
- バリデーションエラー表示

**resources/views/components/delete-confirm.blade.php**
- 削除確認モーダル（Alpine.js使用）

### 4. ルーティングの設定
```php
// routes/web.php
Route::prefix('group/{token}/children')->group(function () {
    Route::get('/', [ChildrenController::class, 'index'])->name('children.index');
    Route::get('/create', [ChildrenController::class, 'create'])->name('children.create');
    Route::post('/', [ChildrenController::class, 'store'])->name('children.store');
    Route::get('/{child}/edit', [ChildrenController::class, 'edit'])->name('children.edit');
    Route::put('/{child}', [ChildrenController::class, 'update'])->name('children.update');
    Route::delete('/{child}', [ChildrenController::class, 'destroy'])->name('children.destroy');
});
```

### 5. 既存APIとの連携
- 既存の`CreateChildController`, `UpdateChildController`, `DeleteChildController`を参考
- フォームバリデーションは既存のFormRequestを再利用

### 6. 削除確認の実装
Alpine.jsを使用して削除確認モーダルを実装：
```blade
<div x-data="{ showModal: false }">
    <button @click="showModal = true" class="px-4 py-2 bg-red-500 text-white rounded">
        削除
    </button>

    <div x-show="showModal" class="fixed inset-0 bg-gray-600 bg-opacity-50">
        <div class="bg-white p-6 rounded-lg">
            <h3>本当に削除しますか？</h3>
            <form method="POST" action="{{ route('children.destroy', ['token' => $token, 'child' => $child->id]) }}">
                @csrf
                @method('DELETE')
                <button type="submit" class="px-4 py-2 bg-red-500 text-white rounded">
                    削除する
                </button>
                <button @click="showModal = false" type="button" class="px-4 py-2 bg-gray-500 text-white rounded">
                    キャンセル
                </button>
            </form>
        </div>
    </div>
</div>
```

### 7. テスト
- 子ども一覧が表示される
- 子どもを追加できる
- 子ども情報を編集できる
- 子どもを削除できる（確認モーダル表示）
- バリデーションエラーが正しく表示される

## 完了条件
- [ ] ChildrenController実装完了
- [ ] ビュー作成完了
- [ ] コンポーネント作成完了
- [ ] ルーティング設定完了
- [ ] 削除確認モーダル実装完了
- [ ] 動作テスト完了

## 備考
- グループトークンでのアクセス制御を確認
- 存在しないグループ/子どもへのアクセス時のエラーハンドリング
