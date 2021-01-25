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

### Query Builder

- SQL のクエリ文を生成するために用意された一連のメソッド
  - Java の MyBatis にもあるやつっぽい
- PHPの処理としてはメソッドを呼び出していくだけ

```php
// DB::table() で対象となるテーブルを指定
// get()でtable の全レコードを取得する
DB::table('table_name')->get();
// where() で where 文。第一引数に column名、第二引数に演算記号（省略可）、第三引数に値を指定
// first() で先頭を取得
DB::table('table_name')->where('id', $id)->first();
// 複数の条件のうち、いずれかにマッチしたとしたい場合は where() を使った後、orWhere() を使う
// 全てにマッチするものとしたい場合は where() でつなげていく
DB::table('table_name')->where(...)->orWhere(...);
DB::table('table_name')->where(...)->where(...);
// whereRaw を使うことで、検索の条件を文字列で指定できる
// ? には 第二引数の array 内の変数が 1 つずつ挿入される
DB::table('table_name')->where('age >=  ? and age <= ?', [$min, $max])->get();
// orderBy で並び順を指定(default は asc（昇順）でその場合第二引数は必要ない)
DB::table('table_name')->orderBy('column', 'asc')->get();
DB::table('table_name')->orderBy('column', 'desc')->get();
// offset: 指定したい位置からレコードを取得
// limit: 指定した数だけレコードを取得
DB::table('table_name')->offset(number)->limit(number)->get();
// insert
// column => value の連想配列を引数に与える
DB::table('table_name')->insert([
  'name' => '...',
  ...
]);
// update
// where で絞り込んでから使う
DB::table('table_name')->where(...)->update([
  'name' => '...',
  ...
]);
// delete
DB::table('table_name')->where(...)->delete();
```

## migration

- Laravel の DB migration は以下の手順で行う
  1. 専用のスクリプトファイルの作成(コマンド)
  2. スクリプトの記述
  3. migration 実行

### migration の実行方法

マイグレーションには以下のコマンドを使う

```sh
# database/migrations/yyyy_MM_dd_hhmmss_file_name.php というファイルができる
# ただし、時刻は標準時
php artisan make:migration file_name
# migration
# ただし、sail を使って環境構築した場合は ./vendor/bin/sail artisan migrate
php artisan migarte
```

以下のようなファイルが作成される

```php:database/migrations/yyyy_MM-dd_hhmmss_create_people_table.php
// create_people_table migration を作成
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreatePeopleTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('people', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('people');
    }
}
```

- デフォルトで `users` や `password` といったマイグレーションファイルが作成されている。これらは認証認可で利用する
- `up` はテーブル作成時、`down`はテーブル削除時の処理を記述
  - `up`
    - `up`内では、`Schema::create`でテーブルを作成している
    - クロージャ内の定義を変更することで、テーブル定義を変更できる
    - 定義には `Blueprint` クラスを使う
  - `down`
    - `Schema::drop('table_name')` または `Schema::dropIfExists('table_name')`を使う

### Blueprint class でのフィールド定義の一部

|フィールド|概要|
|---|---|
|integer(field_name)|整数|
|float(field_name)|実数|
|string(field_name)|テキスト|
|boolean(field_name)|真偽値|
|dateTime(field_name)|日時|
|timestamps()|created_atとupdate_at が自動生成される|

## Seeding

- 初期状態でダミー用のレコードを用意しておくことを Seeding という。
- Seeding は Laravel では以下の方法で実行できる
  1. スクリプトファイルの作成（コマンド）
  2. スクリプトファイルの編集
  3. コマンドでseedingを実行

### Seeding の実行方法

seedingには以下のコマンドを使う

```sh
# seeder(seedingのためのスクリプト)を作成
php artisan make:seeder seeder_name
# seeding
# ただし、sail を使って環境構築した場合は ./vendor/bin/sail artisan db:seed
php artisan db:seed
```

- 以下のようなファイルが作成される。
- `DB::table(...)->insert()` などを使って挿入したいデータをいれる

```php:database/seeders/PeopleTableSeeder.php
// make:seeder PeopleTableSeeder を実行
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class PeopleTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        //
    }
}
```

- 上記ファイルを編集した後、`database/seeders/DatabaseSeeder.php` を編集して、seeder を反映させる
- `$this->call(PeopleTableSeeder::class);` を `run()` の中に記述すれば良い

```php:database/seeders/DatabaseSeeder.php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     *
     * @return void
     */
    public function run()
    {
        // \App\Models\User::factory(10)->create();
    }
}
```
