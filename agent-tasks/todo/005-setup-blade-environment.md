# タスク005: Blade開発環境のセットアップ

## 目的
Blade + Alpine.js/Livewire + Tailwind CSSの開発環境を構築

## 作業内容

### 1. Tailwind CSSのインストール
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### 2. tailwind.config.jsの設定
```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./resources/**/*.blade.php",
    "./resources/**/*.js",
    "./resources/**/*.vue",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

### 3. app.cssの設定
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### 4. Alpine.jsのインストール（オプションA選択時）
```bash
npm install alpinejs
```

resources/js/app.jsに追加：
```javascript
import Alpine from 'alpinejs'
window.Alpine = Alpine
Alpine.start()
```

### 5. Livewireのインストール（オプションB選択時）
```bash
composer require livewire/livewire
php artisan livewire:publish --config
```

### 6. vite.config.jsの確認
```javascript
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
});
```

### 7. レイアウトファイルの作成
`resources/views/layouts/app.blade.php`を作成：
```blade
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="csrf-token" content="{{ csrf_token() }}">
    <title>{{ config('app.name', 'mu-total') }}</title>
    @vite(['resources/css/app.css', 'resources/js/app.js'])
</head>
<body>
    <div id="app">
        @yield('content')
    </div>
</body>
</html>
```

### 8. 動作確認用ページの作成
`resources/views/welcome.blade.php`を更新：
```blade
@extends('layouts.app')

@section('content')
<div class="container mx-auto px-4 py-8">
    <h1 class="text-4xl font-bold text-blue-600">mu-total</h1>
    <p class="mt-4 text-gray-600">Tailwind CSS is working!</p>

    @if(/* Alpine.js使用時 */)
    <div x-data="{ open: false }">
        <button @click="open = !open" class="mt-4 px-4 py-2 bg-blue-500 text-white rounded">
            Toggle
        </button>
        <div x-show="open" class="mt-2 p-4 bg-gray-100">
            Alpine.js is working!
        </div>
    </div>
    @endif
</div>
@endsection
```

### 9. ビルドとテスト
```bash
npm run dev
php artisan serve
```

ブラウザで http://localhost:8000 にアクセスし、Tailwind CSSとAlpine.jsが動作することを確認

## 完了条件
- [ ] Tailwind CSSインストール完了
- [ ] Alpine.js/Livewireインストール完了
- [ ] vite.config.js設定完了
- [ ] レイアウトファイル作成完了
- [ ] テストページ作成完了
- [ ] ビルド成功
- [ ] ブラウザで動作確認完了

## 備考
- npm run devを起動したまま開発を進める
- ホットリロードが効くことを確認
