# Assets

## Static File

- 静的ファイルは `public` 以下に配置する
- 参照は `<img /***.png>` のようにどこからでも`public`をルートとして参照できる
- AWS S3 に配置することもできる

## CSS styling

### CSS in JSX

- 標準で [styled-jsx](https://github.com/zeit/styled-jsx)をサポートしている
- [styled-components](https://github.com/zeit/next.js/tree/canary/examples/with-styled-components), [emotion](https://github.com/zeit/next.js/tree/canary/examples/with-emotion) を利用することもできる

```jsx
<style jsx>{`
  ...
`}
```

### CSS を単体で書く場合

1. `components` ディレクトリにCSSを反映させたいコンポーネントを作成する(`components/layout.js`とする)
2. `components/layout.module.css`にcssを記述する（ファイル名は `***.module.css`であること）
3. `components/layout.js` でcssファイルをインポートして、スタイルを反映させる

```css
.container {
  max-width: 36rem;
  padding: 0 1rem;
  margin: 3rem auto 6rem;
}
```

```jsx
import styles from './layout.module.css';

// css のクラスを className に挿入（React と同じ）
export default function Layout({ children }) {
  return <div className={styles.container}>{children}</div>;
}
```

- sass を使いたいときは `sass` ライブラリを node でインストールしておけば良い
  - 拡張子を `.module.scss` など末尾を利用したい sass の拡張子にしておくこと

#### Global Styling

アプリケーション全体のスタイリングを定義したいときは `pages/_app.js` を作成し、その中で css ファイルを読み込む

```jsx:pages/_app.js
// pages/_app.js
import '../styles/global.css'

export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

## References

- [Static File Serving](https://nextjs.org/docs/basic-features/static-file-serving)
- [classnames](https://github.com/JedWatson/classnames)
  - cssを反映させるクラス名の切り替えを簡単にするライブラリ
