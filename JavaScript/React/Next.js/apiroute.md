# API Route

NextjsはAPI Routeをサポートしているため、APIエンドポイントをNode.jsの関数として簡単に作成できる

## 作成方法

`pages/api` ディレクトリの中に以下のフォーマットを持つ関数を定義した JavaScript ファイルを作成する

```js
// req: http.IncomingMessage, res: http.ServerResponse
export default (req, res) => {
  // res.status(200).json({text: 'Hello'})
  // 上記を定義すると、/api/*** にアクセスすると {"text": "Hello"} が返る
}
```

## 注意点

- `getStaticProps`, `getStaticPaths` からAPI RouteをFetchしてはならない
  - 上記で実装するなら、`getStaticProps`の中やヘルパーメソッドでサーバーサイドのコードを書く
  - これらのメソッドはサーバーサイドで実行されるメソッドなので、同じアプリの中でクライアントサイドに公開しているAPIをサーバーサイドから呼び出す意味はない
- フォーム入力の処理に使うのが良い
- 他のRouteと同じように動的API Routeを用意することもできる（Dynamic API Routes）

## References

- [API Routes](https://nextjs.org/docs/api-routes/introduction)
- [Dynamic API Routes](https://nextjs.org/docs/api-routes/dynamic-api-routes)
