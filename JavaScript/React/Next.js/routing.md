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

## References

- [Link(API Reference)](https://nextjs.org/docs/api-reference/next/link)
- [Routing(API Reference)](https://nextjs.org/docs/routing/introduction)
