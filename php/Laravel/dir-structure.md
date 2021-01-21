# Dir structure

Laravel のディレクトリ構成についてまとめる  
ディレクトリ構成は以下の通り

```sh
.
├── app       # アプリケーションのコアコードを配置。ほとんどのクラスはここに配置
├── bootstrap # フレームワークの初期化を行う`app.php`と初期化処理のパフォーマンス最適化のための`cache`を配置(FWが生成するルート、サービスのキャッシュを保存)
├── config    # アプリケーションの全設定ファイル
├── database  # DBのマイグレーション、モデルファクトリ、初期値設定。SQLiteもここに配置することができる
├── public    # アプリケーションへの全リクエストの入り口。`index.php`とアセットも配置
├── resources # publicに配置されるアセットの元ファイルや全ての言語ファイルを配置
├── routes    # 以下で解説。全ルート定義により構成
├── storage   # コンパイルされたBladeテンプレート、ファイルキャッシュなど、FWにより生成されるファイルが保存
├── tests     # テストコード。サフィックスとして`Test`をつける。phpunit でテストを実行
└── vendor    # パッケージ管理ツール `Composer` による依存パッケージを配置
```

## app

デフォルトで namespace `App` の元におかれていて、`Composer`がオートロードしている  
`Comsole`, `Http`, `Providers`などの多彩なサブディレクトリを保持。`Console`と`Http`はアプリケーションの「コア」部分へAPIを提供。`Console`は全Artisanコマンドで構成され、`Http`はコントローラ、フィルター、リクエストにより構成  
Artisanコマンド`make`により、様々なディレクトリがこのディレクトリ内に生成される。例えば、`php artisan make:Job` コマンドを実行すると、`app/Jobs`ディレクトリが作成される

### Broadcasting
`php artisan make:channel`で作成。アプリケーションの全ブロードキャストチャンネルクラスで構成。デフォルトでは存在しないが、最初にチャンネルを生成したときに作成

### Console
`php artisan make:command`で作成。アプリケーションの全カスタムArtisanコマンドで構成。カスタムコマンドやタスクのスケジューリングを登録

### Events
`php artisan make:generate`または`php artisan make:event`コマンド実行時に作成（デフォルトでは作られていない）。イベントクラスを設置する場所。イベントは特定のアクションが起きたことをアプリケーションの別の部分へ知らせるために使う

### Exceptions
例外ハンドラで構成

### Http
コントローラ、ミドルウェア、フォームリクエストを設置。リクエストを処理するロジックはほとんどこのディレクトリ 内に設置

### Jobs
`php artisan make:job`コマンド実行時に生成（デフォルトで作られていない）。アプリケーションのキュー投入可能なジョブを配置する場所。アプリケーションによってキューに投入されるか、か現在のリクエストサイクル中に同期的に実行される。現在のリクエストサイクル中に同期的に実行するジョブは、コマンドパターンを実装しているため、時に「コマンド」と呼ばれることがある。

### Listeners
`php artisan event:generate` or `php artisan make:listener`で生成（デフォルトで作られていない）。イベントを処理するクラスを設置。イベントリスナーはイベントのインスタンスを受け取り、発行されたイベントへ対応するロジックを実行。(`UserRegistered`は`SendWelcomeEmail`で処理されるなど)

### Mail
`php artisan make:email`で生成（デフォルトでは作られていない）。アプリケーションから送信されるメールを表す全クラスで構成。メールを組み立てるロジックを1つのシンプルなクラスへカプセル化できる。

### Models
全てのEloquentモデルクラスで構成。Eloquent ORMはActiveRecordの実装を提供

### Notifications
`php artisan make:notification`によって作成（デフォルトで作られていない）。アプリケーションから送られる通知を実装する。メール、Slack, SMS, DBの保存など様々なドライバに対する通知の送信に対応

### Policies
`php artisan make:policy`によって作成（デフォルトで作られていない）。アプリケーションの認可ポリシークラスを設置。

### Providers
アプリケーションの全サービスプロバイダによって構成。サービスプロバイダはサービスをコンテナと結合、イベントの登録、アプリケーションへやってくるリクエストを処理するために必要な用意をするタスクを実行するなどといった、アプリケーションの事前準備を行う。

### Rules
`php artisan make:rule`によって作成（デフォルトで作られていない）。アプリケーションが使用するバリデーションルールオブジェクトで構成



## Others

### References
- https://readouble.com/laravel/8.x/ja/structure.html
