# タスク006: グループ管理UIの実装（Blade）

## 目的
SvelteKitで実装されていたグループ作成・表示機能をBladeで再実装

## 作業内容

### 1. コントローラーの作成/調整
```bash
# 既存のAPIコントローラーを確認
# 必要に応じてWebコントローラーを作成
php artisan make:controller Web/GroupController
```

GroupControllerに以下のメソッドを実装：
- `create()` - グループ作成フォーム表示
- `store()` - グループ作成処理
- `show($token)` - グループ情報表示

### 2. ビューの作成

**resources/views/group/create.blade.php**
- グループ名入力フォーム
- 作成ボタン
- バリデーションエラー表示

**resources/views/group/show.blade.php**
- グループ名表示
- 共有トークン/URL表示
- 子ども一覧表示
- 子ども追加ボタン

### 3. コンポーネントの作成

**resources/views/components/group-form.blade.php**
```blade
<div class="max-w-md mx-auto">
    <form method="POST" action="{{ route('group.store') }}">
        @csrf
        <div class="mb-4">
            <label for="group_name" class="block text-sm font-medium text-gray-700">
                グループ名
            </label>
            <input
                type="text"
                name="group_name"
                id="group_name"
                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm"
                value="{{ old('group_name') }}"
                required
            >
            @error('group_name')
                <p class="mt-1 text-sm text-red-600">{{ $message }}</p>
            @enderror
        </div>
        <button type="submit" class="w-full px-4 py-2 bg-blue-500 text-white rounded">
            グループを作成
        </button>
    </form>
</div>
```

**resources/views/components/share-url.blade.php**
- 共有URL表示
- コピーボタン（Alpine.js使用）

### 4. ルーティングの設定
```php
// routes/web.php
Route::get('/', [GroupController::class, 'create'])->name('home');
Route::post('/group', [GroupController::class, 'store'])->name('group.store');
Route::get('/group/{token}', [GroupController::class, 'show'])->name('group.show');
```

### 5. 既存APIとの連携
- 既存の`CreateGroupController`を利用するか、Web用に新規作成
- フォームバリデーションは`CreateGroupRequest`を再利用

### 6. スタイリング
- モバイルファースト
- Tailwind CSSでレスポンシブ対応
- SvelteKit版のデザインを参考にする

### 7. テスト
- グループ作成フォームが表示される
- グループ名を入力して作成できる
- 作成後、グループページにリダイレクトされる
- 共有URLが表示される
- バリデーションエラーが正しく表示される

## 完了条件
- [ ] GroupController実装完了
- [ ] ビュー作成完了
- [ ] コンポーネント作成完了
- [ ] ルーティング設定完了
- [ ] スタイリング完了
- [ ] 動作テスト完了

## 備考
- SvelteKitのコードを参考にしつつ、Bladeの方式で実装
- 既存のビジネスロジックは可能な限り再利用
