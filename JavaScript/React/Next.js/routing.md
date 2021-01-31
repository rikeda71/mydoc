# Routing

- Next.js ではルーティングを貼りたいコンポーネント（ページ）は`pages`ディレクトリに配置させる
- ページはファイル名に基づいてルーティングに関連づけられる

## Routes

### Index Routes

- `pages/index.js`: `/`
- `pages/blog/index.js`: `/blog`

### Nested Routes

- `pages/dashboard/settings/username.js`: `/dashboard/settings/username`
- `pages/posts/first-post.js`: `/posts/first-post`

### Dynamic Route Segments

[Dynamic Routes Document](https://nextjs.org/docs/routing/dynamic-routes)

- `pages/blog/[slug].js`: `/blog/:slug (/blog/hello-world)`
- `pages/[username]/settings.js`: `/:username/settings (/foo/settings)`
- `pages/post/[...all].js`: `/post/* (/post/2020/id/title)`

#### getStaticPaths, getStaticProps

- Dynamic Routing を実現するには、`[***].js`に `getStaicPaths` と `getStaticProps` を実装する必要がある
  - いずれも `async` メソッド
  - `getStaticPaths` は `[***]` に取り得る値を返すメソッド
    - 返り値に `fallback: false` を加えると`getStaticPaths`で生成されていないリンクに遷移しても404に遷移するようにしてくれる（通常はエラーページに遷移）
    - `getStaticPaths` は本番環境ではビルド時に実行される
  - `getStaticProps` は `params` を引数にとり、`params.***` を使用してレンダリングに必要なデータを取得して返すメソッド

```jsx
// rendring 用のメソッドの引数には `getStaticProps.props` の中で定義されている変数を指定
export default function MethodName({ ~~~ }) {
}

export async function getStaticPaths() {
  // 以下のような形式の値を返す
  /*
  return {
    {
      params: {
        ***: ...
      }
    },
    ...
  }
  */
}

export async function getStaticProps({ params }) {
  // params.*** を使って、rendering 用のメソッドに渡す props を取得
  /*
    return {
      props: {
        ~~~: ...
      }
    }
  */
}
```

## Link Component

- React Router と同じように `<Link>` を使うことで、異なるページへの遷移を実現できる
- `Link` は 2ページ間のクライアントサイドでのナビゲーションを実現している。ページ遷移をJavaScriptで実装しているため高速(ブラウザで別ページにいくより)でページの切り替えができる
- Next.jsで実装したアプリ内の遷移のみ`<Link>`を使う。外のページへの遷移は`<a>`を使う

```jsx
import Link from 'next/link'
...
// nested routes
<Link href="/path/to/route">Text</Link>
// dynamic routes
<Link href={`/blog/${encodeURIComponent(post.slug)}`}>
<Link href={{
  pathname: `/blog/[slug]`,
  query: {slug: post.slug},
}}>
```

## Router

`useRouter` を `next/router` からインポートすることで、ルーティングの値を取得できる（[参考](https://nextjs.org/docs/routing/dynamic-routes)）

## 404 page

`pages/404.js` を作成しておくことで、404 が出た時にこのページに遷移する

## References

- [Link(API Reference)](https://nextjs.org/docs/api-reference/next/link)
- [Routing(API Reference)](https://nextjs.org/docs/routing/introduction)
- [Data Fetching](https://nextjs.org/docs/basic-features/data-fetching)
- [Router](https://nextjs.org/docs/routing/dynamic-routes)
