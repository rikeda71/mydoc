# Go Module

Go 1.11 から Go に標準搭載された Go のパッケージ管理システム

## Usage

プロジェクトを作った時に、まずは以下を実行する

```sh
go mod init path/to/project # path/to/project は git のリモートリポジトリ
```

- 以下の `go.mod` ファイルが生成される（`go 1.xx` は 利用する Go のバージョン）
- `go.mod` は手で編集してはいけない

```gomodule:go.mod
module path/to/project

go 1.xx
```

以下のコマンド類で操作する

```sh
go get path/to/library # go mod のコマンドではないが、go get でライブラリのインストール・ビルドが行われ、$GOPATH/pkg に使用しているバージョンごとに保存される。`go.sum` が生成される
go get -u path/to/library # ライブラリのバージョンの更新
go mod tidy # 使用していないライブラリを$GOPATH/pkgから削除(go.modの管理下からも消される)
go mod vendor  # Go 1.11 以前のコンパイラを使ったビルド用に vendor ディレクトリを作成
go mod edit # go.mod を編集する。go のバージョンやライブラリのバージョンを変更
go mod graph # ライブラリ間の依存関係を出力
go mod verify # ダウンロードした依存パッケージが正しいか検証
```

## References

- [Go Module Github Wiki](https://github.com/golang/go/wiki/Modules)
- [Go Module Reference](https://golang.org/ref/mod)
- [Using Go Modules - The Go Blog](https://blog.golang.org/using-go-modules)
