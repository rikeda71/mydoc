# Rest API

## Laravel での APIの提供

Controller でモデルの配列を返却すれば JSON を返却できる

```php
public function index()
{
  $item = Model::all();
  return $item->toArray();
}

// 要素が1つだけだったら、JSON の配列は取り除かれる `{}` が一番外のカッコになる
public function show($id)
{
  $item = Model::find($id);
  return $item->toArray();
}
```

## resource オプション

- `make:controller` に `--resource` オプションをつけると、CRUD関連のメソッドが一括して作成される

```sh
php artisan make:controller コントローラ名 --resource
```

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class ***Controller extends Controller
{
    public function index()
    {
    }

    public function create()
    {
    }

    public function store(Request $request)
    {
    }

    public function show($id)
    {
    }

    public function edit($id)
    {
    }

    public function update(Request $request, $id)
    {
    }

    public function destroy($id)
    {
    }
}
```

上記コマンドで作成したコントローラは以下を`web.php`に記述することで、1行で全ての操作を紐づけることができる

```php:routes/web.php
Route::resource('rest', 'APP\Http\Controllers\***Controller');
```

|URI|method|HTTP method|
|---|---|---|
|/***|index|GET
|/***/create|create|GET
|/***|store|POST
|/***/:id/show|show|GET
|/***/:id/edit|edit|GET
|/***/:id|update|PUT・PATCH
|/***/:id|delete|DELETE

- 上記表のうち、`/***/create` と `/***/:id/edit` は view を表示するためのメソッドに対応するため、RESTful API の開発には必要ない