# Controller

MVC の Cにあたる。RoRやDjangoと同じ

## Controller の作成

`artisan(アーティザン)`コマンドを使うと雛形を自動生成できる

```sh
php artisan make:controller コントローラ名
```

- 作成されるController

```php:app/Http/Controllers/コントローラ名.php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class コントローラ名 extends Controller
{
    //
}
```

- Controller は namespace `App\Http\Controllers` に作成する
- デフォルトで `Illuminate\Http\Request` をインポート（`use`）しているが、多用するのでふくまれている

## Action

Controller に含まれる処理を **Action** と呼ぶ

- Action の例

```php:app/Http/Controllers/コントローラ名.php
class コントローラ名 extends Controller
{
  public function index() {
    return ***
  }

}
```

- 上記を記述した後、ルート情報をコントローラに紐付ける

```php:routes/web.php
// index action を用意した場合
Route::get('hello', 'App\Http\Controllers\コントローラ名$index');
```

- ルートパラメータも利用できる

```php:app/Http/Controllers/コントローラ名.php
// デフォルトパラメータも引数から指定できる。
class コントローラ名 extends Controller
{
  public function index($id, $pass='pass') {
    return ***
  }
}
```

### Action と Endpoint の紐付け

- Laravel の世界では Controller, Action と Endpoint の関係は `/Controller/Action` とする
- Endpoint をこの命名規則で routes に設定しておくと良い

## Single Action Controller

- 1 Controller, 1 Action の構成とするときの記法
- Action を `__invoke()` とする
  - `__invoke()` はPHPのクラスに用意されているマジックメソッドの1つ
  - マジックメソッドは　`__` から始まる（python の `__***__` みたいなものっぽい）
  - 一般的にPHPでは一般的なクラスでも「インスタンスをそのまま関数のように実行させたい」場合に利用
- routes でアクションを指定しなくて良くなる

```php:app/Http/Controllers/SingleActionController.php
class SingleActionController extends Controller
{
    public function __invoke() {
      return ***
    }
}
```

```php:routes/web.php
Route:get('single', 'App\Http\Controllers\SingleActionController')
```

## Request / Response

- request, response オブジェクトを使うためには以下のようにするだけで良い
- 引数に `Illuminate\Http\Request`, `Illuminate\Http\Response` のパラメータを追加する
  - これは DI(Dependency Injection) によるメソッドインジェクションで実現されている

```php:app/Http/Controllers/コントローラ名.php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Http\Response;

class コントローラ名 extends Controller
{
  public function index(Request $request, Response $response) {
    ***
    return $response;
  }
}
```

### Request の主なメソッド

- `$request->url()`
  - アクセスしたURLを返す。クエリ文字列は省略
- `$request->fullUrl()`
  - アクセスしたURLをクエリ文字列を含めて返す。
- `$request->path()`
  - ドメイン下のパス部分を返す
- `$request->merge(連想配列)`
  - フォームの送信などで送られる値に新しい値を追加する
  - `$request->連想配列のkey` で value を呼び出せる

### Response の主なメソッド

- `$response->status()`
  - ステータスコードを返す
- `$response->content()`
  - コンテンツを取得
- `$response->setContent(value)`
  - コンテンツを設定
