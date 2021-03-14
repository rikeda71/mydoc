# Prerendering

- Next.jsでは全てのページをPrerenderingする。予め各ページのHTMLを生成しておくことでパフォーマンスとSEOが向上する
- 生成された各HTMLはページに必要な最小限の JS コードと関連づけられ、ブラウザによってページが読み込まれるとそのページのコードが実行される（ハイドレーション）
- Prerendering の方法としては **Static Generation(Static Site Generator)** と **Server Side Rendering** がある
  - Next.jsではこのどちらの方法を使うかをページごとに選択できる
  - Static Generation は Next.js の文脈で使う言葉。一般的には Static Site Generator(SSG)と呼ばれている

## Static Generation

ビルド時にHTMLが生成され、リクエストが来たら生成済みのHTMLを再利用する方法

用途

- LP
- ブログ記事
- ヘルプ・ドキュメント

### getStaticProps

Static Generation に外部データ（本番DBや外部API）が必要な場合に使うメソッド

```jsx
// 以下のように props を返すメソッドを定義
// props に含まれる値を対象のコンポーネントを定義したメソッドの引数に追加(今回だと keyValuePair を引数に追加)
export async function getStaticProps() {
  ...

  return {
    props: {
      keyValuePair
    }
  }
}
```

- `getStaticProps` の中で外部APIに問い合わせる、DBにクエリを発行してデータを取得することもできる
  - server side で実行されるため、セキュリティ上の問題（ブラウザから実行される、ブラウザから取得処理が渡されること）もない
- ビルド時のみ実行されるので、クエリパラメータ・HTTPヘッダを可変にすることはできない
- ページでのみ使える。コンポーネントでは使えない

## Server Side Rendering (SSR)

リクエストごとにHTMLが生成される

用途

- ユーザーのリクエストを受け付けて生成されるページ

### getServerSideProps

Next.jsでSSGを使う場合に定義するメソッド。リクエスト毎にデータを取得しなければならないページのレンダリングに使う

```jsx
// context はリクエストの特定パラメータ
// 例えば dynamic routing で当てはめられた値と要素の key-value、locale などが含まれる
export async function getServerSideProps(context) {
  return {
    props: {

    }
  }
}
```

## Client Side Rendering

Prerendering が必要ない場合はページ内でレンダリング処理を行う

- 外部データを必要としないページをPrerenderingする
  - 外部データが必要な場合（静的ファイルや外部APIを読み込む場合）はSSGを使うと効果的
- ページが読み込まれた後に外部データを取得して埋め込む
  - SSR で代用可
  - 実装はSSRより簡単
  - SEO に関係ないページ（例えばユーザページ）はこのSSRを使う必要がない
- ページが読み込まれた後、ユーザーの操作に合わせてレンダリングを変える

### Stale While Revalidate (SWR)

- データフェッチ用のReact Hook。キャッシング、再検証、フォーカストラッキング、インターバルを開けた再Fetchなどに対応しており、クライアントサイドレンダリングで有用
- Next.js を開発している Vercel社製のライブラリ

```jsx
import useSWR from 'swr'

function Profile() {
  const fetcher = (url: string): Promise<any> => fetch(url).then(res => res.json());
  const { data, error } = useSWR('/api/user', fetcher)

  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <div>hello {data.name}!</div>
}
```

## References

- [getStaticProps](https://nextjs.org/docs/basic-features/data-fetching#getstaticprops-static-generation)
- [getServerSideProps](https://nextjs.org/docs/basic-features/data-fetching#getserversideprops-server-side-rendering)
- [SWR](https://swr.vercel.app/)
- [SWR Zenn(Japanese)](https://zenn.dev/uttk/articles/b3bcbedbc1fd00)