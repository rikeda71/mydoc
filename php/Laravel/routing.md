# Routing

- ルーティング情報は `routes` に配置する
- web上に公開するものは`routes/web.php`に書く

```php
Route::get(address, methodなど)
```

- method には例えば `view(template_name)` がある
  - view(template\_name) で `resources/views/welcome.blade.php` を呼び出せる

## Route parameter

```php
// Route parameter をこのようにして利用
Route::get('/hello/{id}/{pass}', function($id, $pass) {
    ...
});

// 必須じゃない時は ? をつけてデフォルト値を設定
Route::get('/hello/{msg?}', function($msg='no message.') {
    ...
});
```
