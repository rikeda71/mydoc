# Database

## DB利用のための手続き

- Laravel で利用するDBの情報は `config/database.php` に記載されている
- どのDBを利用するかは環境変数 `DB_CONNECTION` に保存されており、Laravel プロジェクトを作成した時に `.env` に記載されているので、利用したいDBを変更する場合、`.env`内の環境変数を修正する

```php:config/database.php
<?php

return [
  'default' => env('DB_CONNECTION', 'mysql'),

  'connections' => [

    'sqlite' => [...],

    'mysql' => [...],

    'pgsql' => [...],

    'sqlsrv' => [...],
  ],
  ...
]
```

## DBとの接続

### DBクラス

- select, insert, update, delete を SQL を使って操作できる

```php
use Illuminate\Support\Facades\DB;
...
$items = DB::select('select * from people');

$param = [
  'id' => 1,
  'name' => 'taro',
  'mail' => 'example.com'
  'age' => 24,
]
DB::insert('insert into people (name, mail, age) values (:name, :mail, :age)', $param);
DB::update('update people set name = :name, mail = :mail, age = :age where id = :id', $param);
DB::delete('delete from people where id = :id', $param);
```

- **パラメータ結合**を使って以下のように書ける
  - `:id` はパラメータの値をはめ込むプレースホルダ

  ```php
  $param = ['id' => $request->id];
  $items = DB::select('select * from people where id = :id', $param);
  ```
