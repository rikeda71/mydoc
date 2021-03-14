# Next.js with TypeScript

## Usage

1. `tsconfig.json`を空ファイルで生成
2. `npm install --save-dev typescript @types/react @types/node` でTypeScriptをインストール
3. `npm run dev` を実行

`npm run dev` の実行後、`tsconfig.json`に設定が記述され、Next.js用の型定義である`next-env.d.ts`ファイルが作成される

## Types with Next.js

|method or argument|type|
|---|---|
|getStaticProps|GetStaticProps|
|getStaticPaths|GetStaticPaths|
|getServerSideProps|GetServerSideProps|
|req(API Route)|NextApiRequest|
|res(API Route)|NextApiRequest|
|{Component, pageProps}(`pages/_app.tsx`)|AppProps|

```tsx
import { GetStaticProps, GetStaticPaths, GetServerSideProps } from 'next'

export const getStaticProps: GetStaticProps = async context => {
}

export const getStaticPaths: GetStaticPaths = async () => {
}

export const getServerSideProps: GetServerSideProps = async context => {
}

// ---------------------

import { NextApiRequest, NextApiResponse } from 'next'

export default (req: NextApiRequest, res: NextApiResponse) => {
}

// ---------------------

import { AppProps } from 'next/app'

export default function App({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />
}
```

## References

- [TypeScript(Next.js Official)](https://nextjs.org/docs/basic-features/typescript)
