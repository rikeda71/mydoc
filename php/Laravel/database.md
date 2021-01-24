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

- select

```php
use Illuminate\Support\Facades\DB;
...
$items = DB::select('select * from people');
...
```
