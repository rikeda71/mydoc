# Validation

与えられたデータが正しいか検証する機能。一般的な validation と一緒

## ValidateRequests

- Controller には `ValidateRequests` というトレイト（メンバーをまとめてクラスに追加するためのもの）が組み込まれている
- 上記により、Controllerに `validate` メソッドが実装されていて、これをつかう

```php
$this->validate($request, [検証項目のarray]);
// 検証項目のarrayは例えば以下のようになっている
/*
[
  'name' => 'required', // 必須
  'mail' => 'email',    // email formatか
  'age' => 'numeric|between:0,150' // 数字かつ0~150の間
]
*/
```

## FormRequest

- リクエストをフォーム利用のために拡張したもの
- クライアントからのリクエスト時点でバリデーションをかける
  - `validate` はコントローラでバリデーションを委任しているため、処理を忘れやすい
- 下記コマンドでFormRequestの雛形を `app/Http/Requests/` 以下に自動生成できる

```sh
php artisan make:request リクエスト名
```

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class リクエスト名 extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return false;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            //
        ];
    }
}
```

- `authorize`：フォームリクエストの許可されているかどうか。戻り値で `true` を返せば許可
- `rules`：適用されるバリデーションの検証ルールを設定。バリデーションルールの連想配列を返却する
- `messages`:バリデーションメッセージを変更

```php
...
    public function authorize()
    {
      return $this->path() == 'hello';
    }

    public function rules()
    {
        return [
            'name' => 'required',
            'mail' => 'email',
            'age' => 'numeric|between:0,150',
        ];
    }

    public function messages()
    {
      return [
        'name.required' => '名前は必ず入力しろ',
      ];
    }
...
```

- このリクエストを Controller の Action の引数に追加

```php
...
   public function post(HelloRequest $request)
   {
     ...
   }
...
```

## Validator

- `validate`, `FormRequest` によるバリデーションでは以下の問題がある
  - フォームの値にしかバリデーションをかけれない
  - バリデーションでエラーを出した時に処理を分岐させれない（バリデーション後の処理の自由度が低い）
- 上記の問題点を解決するために `Validator` が使える

```php
...
public function post(Request $request)
{
  // リクエストクエリにバリデーションをかけたい時は以下のようにすれば良い
  // $validator = Validator::make($request->quer(), ...)
  $validator = Validator::make($request->all(), [
      'name' => 'required',
      'mail' => 'email',
      'age' => 'numeric|between:0,150'
    ], [
      'name.required' => '~~~',
      ...
    ]);

  if ($validator->fails()) {
    ...
  }
  ...
}
...
```

- 状況に応じてルールを追加する方法として `Validator`クラスには `sometimes` メソッドが実装されている
- `$validator->sometimes( 項目名, ルール名, クロージャ )` というふうにして使える

## Validation Rule

- 最初から用意されているバリデーションの他にバリデーションを自作できる
- その中でも有用なものに `Rule` がある
- `Rule` は以下のコマンドで `app/Rules` 以下に自動生成できる

```sh
php artisan make:rule ルール名
```

```php
<?php

namespace App\Rules;

use Illuminate\Contracts\Validation\Rule;

class Myrule implements Rule
{
    /**
     * Create a new rule instance.
     *
     * @return void
     */
    public function __construct()
    {
      //
    }

    /**
     * Determine if the validation rule passes.
     *
     * @param  string  $attribute
     * @param  mixed  $value
     * @return bool
     */
    public function passes($attribute, $value)
    {
      //
    }

    /**
     * Get the validation error message.
     *
     * @return string
     */
    public function message()
    {
      return 'The validation error message.';
    }
}
```

- `passes`：ルールの通過条件。問題ない場合はtrue, それ以外はfalseを返す
- `message`：問題発生時に返却するメッセージ

## Validation の検証ルール

|validation|詳細|
|---|---|
|accept|true, on, yes, 1といった値かをチェック。主にチェックボックスに利用|
|active_url|アドレスで指定されたドメインが実際に有効なものかをチェック|
|url|URL形式で書かれているか|
|after: 日付|指定した日付より後か|
|after_or_equal: 日付|指定した日付と同じか後か|
|before: 日付|指定した日付より前か|
|before_or_equal: 日付|指定した日付と同じか前か|
|alpha|全てアルファベットか|
|alpha-dash|アルファベット＋ハイフン+アンダーバーのいずれかか|
|alpha-num|アルファベット＋数字のいずれかか|
|array|配列になっているか|
|bail|複数のバリデーションルールが2つの項目に適応されているとき、バリデーションエラーが発生したら他のバリデーションルールの適応を中断|
|between:min,max|min <= value <= max の範囲に収まっているか|
|boolean|値が真偽値か。true, false, 0, 1 などであればOK|
|confirmed|項目が「項目名_confirmation」という名前と同じ値であるか。パスワードの2回入力とかで使う|
|date|入力されたテキストが日付の値として使えるものか|
|date_equals: 日付|入力された日付が指定の日付と同じか|
|date_format: フォーマット|入力された値が指定フォーマットの定義に一致しているか|
|different: フィールド|フィールドと違う値か|
|same: フィールド|フィールドと同じ値か|
|digits: 桁数|指定された桁数と同じか|
|digits_between: min,max|指定された範囲内に収まる桁数か|
|dimensions: 設定内容|イメージファイルなどを設定する場合に用いる|
|distinct|配列内に同じ値がないか|
|email|電子メール形式か|
|exists: table, column|DBを利用する場合に使う。入力された値がテーブルの指定カラムにあるか。あればOK|
|unique: table, column|指定のテーブルの指定のカラムに同じ値が存在しないか|
|file|アップロードに成功したファイルであるか|
|filled|からでないか|
|required|必須|
|image|指定されたファイルが画像ファイルかどうか|
|integer|整数かどうか|
|numeric|数字かどうか|
|json|値がjson文字列か|
|min: value, max: value|指定よりも大きいか/小さいか|
|regix: pattern|指定した正規表現パターンと一致するか|
|nullable|値がnullを許容しているか|
|password|認証されているパスワードと一致するか|
|present|値が存在することをチェック。nullでも可|
|string|文字列か|
