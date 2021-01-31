# Session

## Usage

Laravel でセッションは以下のように使う

### Cookie を利用する場合

```php
$request->session()->put(key, value); // 保存
$request->session()->get(key); // 取得
```

### DB を利用する場合

- `config/session.php` の `driver` の値を DB を利用するように変更

```php:config/session.php
// 以下のように書き換える
return [
  ...
  // 'driver' => env('SESSION_DRIVER', 'file'),
  'driver' => env('SESSION_DRIVER', 'database'),
  ...
]
```

- `.env` の `SESSION_DRIVER` もDBを使うように変更

```env
SESSION_DRIVER=database
```

- その後、session用のテーブルを作成。以下のような内容のファイルが作成される

```sh
php artisan session:table // make:migration ではなくこのコマンドを使う
php artisan migrate
```

```php
class CreateSessionsTable extends Migration
{
    public function up()
    {
        Schema::create('sessions', function (Blueprint $table) {
            $table->string('id')->primary();
            $table->foreignId('user_id')->nullable()->index();
            $table->string('ip_address', 45)->nullable();
            $table->text('user_agent')->nullable();
            $table->text('payload');
            $table->integer('last_activity')->index();
        });
    }

    public function down()
    {
        Schema::dropIfExists('sessions');
    }
}
```
