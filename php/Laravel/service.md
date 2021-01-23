# Service

Service は Laravel に用意されている機能強化のための仕組みの1つ

## Service Container

- DI(Dependency Injection) によって必要に応じて Service と呼ばれるプログラムを自動的に自身の中に取り込み、使えるようにしてくれるシステム
- Service と Service Container によって必要に応じて各種機能をアプリケーション内に取り込む

## Service Provider

- Service を登録するために用意される
- 予め登録しておくための設定ファイルが用意されている
- Laravelに用意されている各種機能も Service + Service Provider を元に作られている

### Service Provider の定義

- 以下のようなクラスとして定義される
  - ServiceProvider を景勝
  - `register`, `boot` メソッドを用意
    - `register`：Service Provider の登録処理
    - `boot`：アプリケーションサービスへのブートストラップ処理（アプリケーションが起動する際に割り込んで実行される処理）。

```php
use Illuminate\Support\Facades\View;
use Illuminate\Support\ServiceProvider;

clas ~~~Provider extends ServiceProvider
{
  public function register()
  {
  }

  public function boot()
  {
  }
}
```

- `artisan` で自動生成できる

```sh
php artisan make:provider プロバイダー名
```

- 処理を記述した後、`config/app.php` に作成した Provider を追加

```php
  ...
  'providers' => [
    ...

    App\Providers\~~~Provider::class,
  ]
  ...
```
