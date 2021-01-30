# PHPUnit

PHPUnit はphpの単体テスト用のライブラリ

## Usage

以下のコマンドでインストール

```sh
composer require --dev phpunit/phpunit ^|version|
composer install
```

composer でインストールされた場合を想定する

```sh
./vendor/bin/phpunit  # 全テストの実行
```

## PHPUnit with Laravel

### テストファイルの作成

以下のコマンドで`tests/Feature/***Test.php` が作成される

```sh
php artisan make:test ***Test # tests/Feature/***Test.php
php artisan make:test ***Test --unit # tests/Unit/***Test.php
```

```php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;

class ***Test extends TestCase
{
    /**
     * A basic feature test example.
     *
     * @return void
     */
    public function test_example()
    {
        $response = $this->get('/');

        $response->assertStatus(200);
    }
}

```

### DB Unit Test

- DBが関係する単体テストの場合、開発用のDBを汚したくない
- `config/database.php` と `phpunit.xml` を編集することで、sqlite のインメモリDBを使えるので、設定しておくと良さそう
- [参考](https://qiita.com/sola-msr/items/b317bb788f21dac176c4)
- DB を使うテストでは `use DatabaseMigrations` をクラス内に定義しておく
  - これがなければマイグレーションされない


## References

- [PHPUnit Reference](https://phpunit.readthedocs.io/ja/latest/index.html)