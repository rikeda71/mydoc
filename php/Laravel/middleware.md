# Middleware

- リクエストを受け取るとコントローラ処理の前後に割り込み、独自の処理(レイヤー層)を追加する仕組み
- 特定のアドレスにアクセスがあると、ルート情報を元にしてのコントローラのアクションを呼び出すが、その前に割り込んでアクションの処理が実行される前後に指定の処理を実行させることができる

## Middleware の作成

- 以下のコマンドで雛形を自動生成できる
- `app/Http/Middleware/` 以下に作成される

```sh
php artisan make:middleware ミドルウェア名
```

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class ミドルウェア名
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle(Request $request, Closure $next)
    {
        return $next($request);
    }
}

```

- 第一引数の`$request`はリクエストの情報を管理するRequestインスタンスが渡される。
- `$next`はClosureクラスのインスタンスで、無名クラスを表すためのクラス
  - `$next` はクロージャで、これを呼び出して実行することで、Middleware からアプリケーションへと送られるリクエストを作成できる

## Middleware の設定

- Middlewareを使うには、`routes/web.php` にルート情報を追加する

```php
Route::get('route', '~~Controller@index')
   ->middleware(~~Middleware::class)->middleware(...);
```

- Middleware は `$next($request)` で処理を Controller または後続の Middleware に渡す
- `$next($request)` を return せず、変数に出力を格納することで、Middlewareを後処理に使うことができる

```php
    public function handle(Request $request, Closure $next)
    {
        $response = $next($request);
        // 何かしらの後処理
        return $response;
    }
```

## Global Middleware

全てのアクセスで自動的に使いたい Middleware がある場合、Global Middleware を使う

### Global Middleware の登録

- `app/Http/Kernel.php` の `protected $middleware` に利用したい Middleware を追加する

```php:app/Http/Kernel.php
  protected $middleware = [
        // \App\Http\Middleware\TrustHosts::class,
        \App\Http\Middleware\TrustProxies::class,
        \Fruitcake\Cors\HandleCors::class,
        \App\Http\Middleware\PreventRequestsDuringMaintenance::class,
        \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
        \App\Http\Middleware\TrimStrings::class,
        \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
        // ここから追加する
    ];
```

### Middleware のグループ登録

- Middleware にはグループ化して登録するための仕組みも用意されている。
- `app/Http/Kernel.php` の `$middlewareGroups` に登録されている

