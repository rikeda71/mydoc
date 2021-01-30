# Eloquent

Eloquent は Laravel の ORM

## Usage

### モデル定義の作成

- テーブルのカラムと紐づくモデルは以下のコマンドで作成する(`app/Models/モデル名.php` が作成される)
- その後、対応するコントローラーやサービス、リポジトリを定義して、DB操作をAPIから行えるようにする

```sh
php artisan make:model モデル名
```

```php:app/Models/モデル名.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class モデル名 extends Model
{
    use HasFactory;
}
```

### モデルの取得

- 1度メソッドを`::`につなげて呼び出した後はアロー演算子で続けてメソッドを呼ぶことができる

```php
Person::all(); // 全レコード取得
Person::find(integer) // 引数で指定したintの値と一致する`$primaryKey` の値を持つモデルを取得。デフォルトはidカラムで比較される
// 指定した field_name カラムでvalueと同じ値を持つモデルを取得
Person::where('field_name', value)->get();
Person::where('field_name', value)->first();

```

- 以下のように自前でメソッドを用意することもできる
- このメソッドは `$items[].getData()` と呼び出すことができる

```php
// ただし、id, name, ageカラムが必要
public function getData(): string
    {
        return $this->id . '.' . $this->name . ' (' . $this->age . ')';
    }
```

#### ローカルスコープ・グローバルスコープ

##### ローカルスコープ

- `scope〇〇` というメソッドをモデル内に実装することで、`〇〇`メソッドをアプリケーション全体で簡単に再利用可能な、一連の共通制約として定義できる（[ローカルスコープ](https://readouble.com/laravel/5.8/ja/eloquent.html#query-scopes)）

```php
// これをモデルに定義しておくことで、モデルの呼び出し側は `name('名前')`を呼び出すことで、名前で絞り込みを行うことができる
public function scopeName($query, $str)
{
  return $query->where('name', $str);
}

// `yangs()` メソッドで 20歳未満で絞り込みができる
public function scopeYangs($query)
{
  return $query->where('age', '<', 20);
}
```

##### グローバルスコープ

- 処理を用意しておくだけで、そのモデルでの全てのレコード取得時にそのスコープが適応されるようになる
- グローバルスコープの作成には `static boot`メソッドを override して `static addGlobalScope` メソッドを使う

```php
protected static function boot()
{
  parent::boot();

  static::addGlobalScope('scope_name', function(Builder $builder) {
    $builder->where('field_name', ...);
  })
}
```

- `Scope`クラスを作ることで、汎用性の高いグローバルスコープの可読性が上がる

```php
class Scope*** implements Scope
{
    public function apply(Builder $builder, Model $model)
    {
        $builder->where(...);
    }
}
```

### モデルの作成・更新・削除

- `$rules` と `$guarded` プロパティをモデルに設定する
  - `$rules` は[バリデーション](validation)を参考。この定数をコントローラ側から参照するだけ
  - `$guarded` は 入力のガードを設定する。ガードは値を用意しないものを設定する。例えば、`id`は autoincrement が設定されることが多いので、モデルの保存時に指定するのではなく、DB側が連番を振るのでガードする

```php
class Person extends Model
{
    use HasFactory;

    protected $guarded = array('id');

    public static $rules = array(
        'name' => 'required',
        'mail' => 'email',
        'age' => 'integer|min|max:150'
    );

    ...

}
```

- 保存処理は以下のようにモデルのインスタンスから`save()`を呼ぶ
  - `save()` で `insert` または `update`ができる（作成も更新も `save()` で実行できる。ただし、更新には PK が必要）
- `削除処理` は PK を格納したモデルのインスタンスで `delete()` を呼ぶ

```php
// 作成・更新
$person = new Person;
$person->id = 1;
$person->name = "name";
$person->save();
// 削除
$person->delete();
```

### Relation

テーブル同士のリレーションを簡単に実装するための仕組みがEloquentには用意されている

#### has One 結合

2つのテーブルが1対1の関係で関連づけられている時に使う

```php
// モデルクラスの中で以下のメソッドを定義する
// 親のモデルの中に子を紐づけるようにして書く
public function モデル名()
{
  return $this->hasOne('App\Models\モデル名')
}
```

#### has Many 結合

2つのテーブルが1対多の関係で関連づけられている時に使う

```php
// モデルクラスの中で以下のメソッドを定義する
// 親のモデルの中に子を紐づけるようにして書く
public function 複数形のモデル名()
{
  return $this->hasMany('App\Models\モデル名')
}
```

#### belongs To 結合

従テーブルから関連づく主テーブルのレコードを取り出すために使う

```php
// モデルクラスの中で以下のメソッドを定義する
// 子のモデルの中に親を紐づけるようにして書く
public function モデル名()
{
  return $this->belongsTo('App\Models\モデル名')
}
```

#### has・doesntHave

関連するレコードの有無判定を行うためのメソッド

```php
// リレーションの値を持つモデルのみ取得
モデル::has('relation_name')->get();
// リレーションの値を持たないモデルのみ取得
モデル::doesntHave('relation_name')->get();
```

#### Eager Loading (N + 1 問題の解決)

- N + 1 問題とは
  - 上記のように関連するレコードを取得した場合、まず、親レコードを取得し、その後に親レコードのモデルに紐づく子レコードを1つずつ取得することになる。
  - 「1 + 親レコードに含まれるモデルの数(N)」だけDBアクセスが行われる
- `with` メソッドを使うことでDBアクセスの回数を減らすことができる
  1. 対象のモデルを取得する
  2. モデルに含まれるキーをひとまとまりにして、モデルに付属するリレーションを1度のDBアクセスで取得する

```php
モデル::with('relation_name')->get();
```

## モデル名

- `User`モデルは`users`テーブルを参照する。このように基本的にはモデルの複数形をテーブル名とする
- `Person` モデルは `people` テーブルを参照することに注意

## Leferences

- [Reference](https://laravel.com/api/5.8/Illuminate/Database/Eloquent.html)
