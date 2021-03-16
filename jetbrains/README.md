# JetBrains IDE

## 共通ショートカット

### 検索

- Shift × 2
  - Search Everything

- Ctrl × 2
  - Run anything

- Cmd + Shift + o : ファイル名指定
- Cmd + shift + f : ファイル内の記述から該当箇所を検索
- Cmd + shift + r : ファイル内の記述から該当箇所を置換
- Cmd + shift + a : アクションの検索（Run など）

- 検索ダイアログで「ファイル名:行数」を打つと、その行数に移動

### Navigation

基本的には **Cmd + 各種メニュー番号**

- Cmd + 1
  - Project タブへ移動
- Cmd + 7
  - file structure
- Cmd + 9
  - Git
- 他にもテストを実行した後
  - Cmd + 4 : Run
  - Cmd + 5 : Debug
- Esc : エディタへ戻る
- Opt + <F1> : 現在開いているファイルのプロジェクト/パッケージへジャンプ

### Switch

- Cmd + e : Recent files
- 「Cmd + e」×2 : Recent Edited
- Shift + Cmd + E : Recently Located
- Ctrl + Tab : 一つ前に切り替え

### Jump

- Ctrl + B
  - 定義・参照ジャンプ(メソッドや変数<->実装)
- Opt + Cmd + <F7>, Opt + <F7>
  - 参照検索
- Cmd + L
  - ファイル内で行指定移動
  - vim keybind だと使わない
- F2
  - エラー箇所へジャンプ
- Cmd + [ : 戻る
- Cmd + ] : 進む


#### Class Jump

- Cmd + U : 親クラス
- Opt + Cmd + U : クラス図を表示してジャンプ

### Format

- Tab or Enter
  - Tab : 置換
  - Enter : 追記
- Shift + Cmd + Enter
  - セミコロンが不足している場合追加、カッコが足りない場合追加など、コードを完成させる
  - Complete Current Statement
- Opt + Cmd + L : コードフォーマットを整える
- Opt + Enter : More Actionの実行
  - 警告がある場所で使える
  - finalをつけるとか、利用していないから削除するとか

### Edit

- （文字列を選択してから）Ctrl + g : マルチカーソル
- （文字列を洗濯してから）Ctrl + Shift + u : 大文字小文字切り替え

### Others

- Cmd + w : 現在のタブを閉じる
- Cmd + Shift + v : コピー履歴から貼り付け
- Cmd + Shift + 「-」 : 実装を閉じる
- Cmd + Shift + 「+」 :  実装を展開
- Opt + Shift + ↑↓ : 選択行を上下移動
- Cmd + / : コメントアウト
- Opt + Cmd + / : コメントアウトブロック
- Shift + Ctrl + D/R : ファイルの実行/デバッグ
- Ctrl + ` : 表示モードの切り替え
  - 中でも 4 view mode で以下のモードを選択される
    1. Presentation Mode : 画面がズームされる
    1. Distraction Free Mode : 画面にコードだけをうつす（集中できる）
    1. Full Display Mode : 全画面表示
    1. Zen Mode : Distraction Free Mode & Full Display Mode

## 参考
- [IntelliJ IDEAのショートカット：トップ15（公式）](https://blog.jetbrains.com/ja/2020/03/12/2848/)
- [IntelliJ shortcut pdf with Japanese](https://qiita.com/cypher256/items/8ce0527a46bd7cfe43eb)
- [忙しい人のためのIntelliJ shortcuts](https://qiita.com/yoppe/items/f7cbeb825c071691d3f2)

