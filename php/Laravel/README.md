# Laravel

- [ディレクトリ構成](dir-structure.md)
- [Routing](routing.md)

## Memo

- すぐにできる環境構築（Dockerが必要）

```
curl -s https://laravel.build/example-app | bash  # "example-app" はなんでもいい
cd example-app
./vendor/bin/sail up  # 起動に必要な docker の設定が走り、docker-compose 上で Laravel が動作
```
