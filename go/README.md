# Golang

- [Go Module](gomod.md)

## Go commands

- プログラムのコンパイル: `go build`
- テストの実行: `go test`
- パッケージのドキュメントの表示: `godoc`
- パッケージの取得: `go get`

## 前提

- セミコロンなし
- tabで空白
- `//`, `/* */` がコメントアウト

## Packages

- Goのプログラムはpackageで構成される
- `main`パッケージから開始される

```go
package main

import (
  "fmt"
  "math/rand"
)

func main() {
   fmt.Println("My favorite number is", rand.Intn(10))
}
```

## imports

- importは以下の2種類の書き方がある

```go
import "fmt"
import "math"
```

```go
# 推奨
import (
    "fmt"
    "math"
)
```

### Exported names

- 最初の文字が大文字で始まる名前は外部のパッケージから参照できる名前（exported name）。
- 例えば、`fmt.Println()`や`math.Pi`など

## Functions

- 関数は、0こ以上の引数をとることができる
- 関数定義は `func 関数名(変数名 型, 変数名 型) 返却値の型 { }`

```go
package main

import "fmt"

func add(x int, y int) int {
  return x + y
}

func main() {
  fmt.Println(add(42, 13))
}
```

### Functions continued

- 関数の2つ以上の引数が同じ型である場合、最後の型を残して、省略して記述できる
- ex. `x int, y int` => `x, y int`

### Multiple results

- 関数は複数の戻り値を返却できる
- 定義は `func 関数名(引数) (戻り値の型, 戻り値の型, ...)`

```go
package main

import "fmt"

func swap(x, y string) (string, string) {
  return y, x
}

func main() {
  a, b := swap("hello", "world")
  fmt.Println(a, b)
}
```

### Named return values

- 関数の戻り値となる変数に名前をつけることができる
- 戻り値に名前をつけると、関数の最初で定義した変数名として扱われる
- この方法を使うと、returnステートメントの後ろに返却する変数を書かなくていい
  - ただし、短い関数でのみ使うこと。関数が長くなると、何を返却しているのかわかりにくい

```go
package main

import "fmt"

func split(sum int) (x, y int) {
  x = sum * 4 / 9
  y = sum - x
  return
}

func main() {
  fmt.Println(split(17))
}
```

### Function values

- 関数も変数であるため、他の変数のように関数を渡すことができる
- また、関数を変数に代入することもできる

```go
func compute(fn func(float64, float64) float64) float64 {
  return fn(3, 4)
}

func main() {
  hypot := func(x, y float64) float64 {
    return math.Sqrt(x*x + y*y)
  }
  fmt.Println(hypot(5, 12))

  fmt.Println(compute(hypot))
  fmt.Println(compute(math.Pow))
}
```

### Closure

- Goの関数はclosure。クロージャは、それ自身の外部から変数を参照できる関数値。

```go
// closureの例
func adder() func(int) int {
  sum := 0
  return func(x int) int {
    sum += x
    return sum
  }
}

func main() {
  pos, neg := adder(), adder()
  for i := 0; i < 10; i++ {
    fmt.Println(
      pos(i),
      neg(-2*i),
    )
  }
}
```

## Variables

- `var`で変数を宣言
- 定義方法
  - `var 変数名 型`
  - `var 変数名 = 値`
  - `var 変数1, 変数2, ... = 値1, 値2`
  - `var 変数1, 変数2, ... 型`
  - `var 変数1, 変数2, ... 型 = 値1, 値2, ...`
  - `変数名 := 値`
  - `変数1, 変数2, ... := 値1, 値2`
    - `:=`は関数の外では使えない

## Basic Types

- bool
- string
- int
  - int8, int16, int32, int64
- uint
  - uint8, uint16, uint32(rune, Unicodeのコードポイントをさす), uint64
- float32, float64

### Zero values

- 変数に初期値を与えずに宣言すると、与えられる値。ゼロ値
  - Golangでは、メモリに載らない変数はない。全て、初期化される（明示的に初期化していない場合に、ゼロ値が入る。）
- 型によって変わる
  - int, floatなどの数値型: 0
  - bool: false
  - string: ""

### Type conversions

- `int(1.1)`や`float(43)`のように特定の値や値を含んだ変数を型変換できる
- 値が自動的にキャストされることはないので、そのような場合に使う。（C言語なら、double型の引数にint型の変数を渡せれたが、Golangでは禁止）

### Type inference

- 型推論。明示的な型を指定せずに変数を宣言する場合、変数の型は右側の変数から推論される
- 数値の場合、`int`, `float64`, `complex128`のいずれかに推論される

```go
i := 42           // int
f := 3.142        // float64
g := 0.867 + 0.5i // complex128
```

## Constants

- `const 変数 = 値`と宣言することで、定数を定義できる
- `:=`を使った定義はできない。　文字列、boolean、数値のみで使える
- ex. `const World = "世界"`

## For

- `{ }`が常に必須であることに注意

```go
sum := 0
for i := 0; i < 10; i++ {
  sum += i
}

// 初期化ステートメントと後処理ステートメントはなくてもOK
// 上記の横につける`;`も不要(`for sum < 1000 { ... }`でOK)。これでwhileを再現
for ; sum < 1000; {
  sum += sum
}
```

### Range

- `range`はforループで使用でき、スライスやマップを一つずつ反復するために利用できる

```go
var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}

func main() {
  // i: インデックス, v: 該当するインデックスの要素のコピー
  for i, v := range pow {
    fmt.Printf("2**%d = %d\n", i, v) // 2**n = 1, 2, 4, 8, ...
  }
}
```

- `_`へ代入することで、インデックスまたは要素のコピーを捨てることができる
- ex. `for i, _ := range pow`, `for _, v := range pow`, `for i := range pow`（インデックスだけ欲しい場合、この記法も許容）

## If Else

- `( )`はいらない`{ }`　が常に必須
- 条件前に評価するための簡単なステートメントを定義可能（ifのスコープ内だけで有効）

```go
if x < 0 {
  return sqrt(-x) + "i"
}

# vはifのスコープ内だけで有効
if v := math.Pow(x, n); v < lim {
  return v
} else {
  ...
}
```

## Switch

- これまでと同様、ステートメント、`;`は省略可能であるため、`switch 変数名 { ... }`でOK
- goのswitchは`break`がいらない。（自動的に提供される）

```go
switch os := runtime.GOOS; os {
case "darwin":
  fmt.Println("OS X.")
case "linux":
  fmt.Println("Linux.")
default:
  fmt.Printf("%s.\n", os)
}
```

- 変数名を省略する記法もある
  - その場合、`case`の右辺は`true`だったら、処理を行う形になる

```go
t := time.Now()
switch {
case t.Hour() < 12:
  fmt.Println("Good morning!")
case t.Hour() < 17:
  fmt.Println("Good afternoon.")
default:
  fmt.Println("Good evening.")
}
```

## Defer

- `defer`は、渡した関数の実行を、呼び出し元の関数の終わりまで遅延させるもの（ただし、評価はされる）
- `defer`へ渡した関数が複数ある場合、呼び出しがスタックされることに注意

```go
func main() {
  defer fmt.Println("world")
  fmt.Println("hello")
}
# 出力
# hello
# world

func main() {
  for i := 0; i < 10; i++ {
    defer fmt.Println(i)
  }
}
# 出力
# 9
# 8
# 7
# ...
```

## Pointers

- ポインタは値のメモリアドレスを指す
- `var p *int` 変数Tのポインタは`*T`型、ゼロ値は`nil`
- `&`オペレータはそのオペランドへのポインタを引き出す
- `*`オペレータは、ポインタの指す先の変数を指す
- ほぼC言語と同じだが、ポインタ演算はないことに注意

```go
i := 42
p = &i
fmt.Println(*p) # 42
*p = 21
fmt.Println(*p) # 21
```

## Structs

- `struct`はフィールドの集まり。構造体
- `{...}`を使って初期化。`.`を使ってアクセス

```go
type Vertex struct {
  X int
  Y int
}

v := Vertex{1, 2}
v.X = 4
fmt.Println(v.X) # 4
# ポインタを使ったアクセス
p := &v
p.X = 1e3 # (*p).Xと同じ。Golangでは、この書き方ができる。
fmt.Println(p.X) # 1000
```

```go
type Vertex struct {
  X, Y int # 変数定義同様、同じ型の場合、この記法が使える
}

var (
  v1 = Vertex{1, 2}  // has type Vertex
  v2 = Vertex{X: 1}  // Y:0 is implicit
  v3 = Vertex{}      // X:0 and Y:0
  p  = &Vertex{1, 2} // has type *Vertex
)

func main() {
  fmt.Println(v1, p, v2, v3)
}
# 出力
# {1 2} &{1 2} {1 0} {0 0}
```

### Abstract fields

- classの継承のように、structのフィールドを継承することができる（匿名フィールド）
- フィールドの共通化のためだけに、struct typeを定義するのはOK

```go
type Human struct {
    name string
    age int
    weight int
}

type Student struct {
    Human
    speciality string
}
```

## Arrays

- 配列。`[n]T`型は、型`T`の`n`個の変数の配列を表す
- `var a [10]int`
- Golangでは、配列の長さを変更することはできない
  - __slice__ を使う

### Slices

- 配列の一部を切り出したもの。
- `[]T`が型Tのスライスを表す
  - `[n]T`は型Tの配列。配列とスライスは区別されることに注意
- ex. `a[1:4]`
- スライスの実態は、配列への参照。スライスは実態を持っておらず、配列の部分列を参照しているだけ。
- スライスの要素を変更すると、対応する配列の要素も変更される（スライスは配列のポインタを持っているイメージ）

```go
func main() {
  primes := [6]int{2, 3, 5, 7, 11, 13}

  var s []int = primes[1:4]
  fmt.Println(s)
}
```

- `[]bool{true, false, true}`で配列リテラルを作成し、それを参照するスライスを作成し返却する
  - 作成される配列リテラルは`[3]bool{true, false, true}`と同じ

- スライスの下限値は0、上限値は参照している配列の長さ
  - pythonのように、`-`が使えないことに注意
- 長さ10の配列に対するスライス`a`において以下は等価
  - `a[0:10]`
  - `a[0:]`
  - `a[:10]`
  - `a[:]`

### スライスの長さとキャパシティ

- スライスは`length`と`capacity`を持っている
- length: スライスに含まれる要素数、capacity: スライスの最初の要素から、参照している配列の末尾要素までの数
  - capacity < lengthにしようとするとエラーが出る。
  - 例えば、capacityをスライス操作によって、3にした後、さらにそのスライスを先頭から4までの長さを持つようにしようとするとエラー
- length: `len`、capacity: `cap`で取得可能
- `nil`スライスの場合、長さとキャパシティは0

### make関数によるスライスの作成

- `make`関数を使用して、スライスを作成できる。これによって、動的にサイズを変更しながら配列を作成できる
- ex. `a := make([]int, 5)` # len(a) = 5
- `make`関数の第3引数には、スライスのキャパシティを設定可能

### append関数

- スライスへ新しく要素を追加する場合、`append`関数を使う

```go
var s []int  # []
// append works on nil slices.
s = append(s, 0) # [0]
s = append(s, 1) # [0, 1]
s = append(s, 2, 3, 4) # [0, 1, 2, 3, 4]
```

## Maps

- `map`はキーと値を関連づける（連想配列）
- ゼロ値は`nil`
- `map[T]K`で定義
- `make`関数で、指定された型のmapを初期化することができる

```go
type Vertex struct {
  Lat, Long float64
}

var m map[string]Vertex

func main() {
  m = make(map[string]Vertex)
  m["Bell Labs"] = Vertex{
    40.68433, -74.39967,
  }
  fmt.Println(m["Bell Labs"])
  // または、以下の方法でも定義できる
  var m = map[string]Vertex{
    "Bell Labs": Vertex{
      40.68433, -74.39967,
    },
    "Google": Vertex{
      37.42202, -122.08408,
    },
  }
}
```

### Mutating Maps

- `m := make(map[string]int)`
- 要素の取得：`elem = m[key]`
- 要素の削除：`delete(m, key)`
- 要素の存在確認：`elem, ok = m[key]`
  - `m`に`key`があれば、`ok == true`、存在しなければ`ok == false`になる

## Methods

- Goにはclassがないが、型にメソッド（method）を定義できる
- メソッドは、特殊なレシーバ引数を関数にとる。また、定義したソースコードの外部から呼び出したい場合、メソッド名の先頭は大文字にする
  - 大文字から始まる関数をメソッドとして共有できる。（Javaでいうpublic methodになる）
- レシーバは、`func`キーワードとメソッド名の間に自身の引数リストで表現し、`型.method`で呼び出せる

```go
type Vertex struct {
  X, Y float64
}

// func (任意の名前 型（struct|type）) メソッド名 返却する型
func (v Vertex) Abs() float64 {
  return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
// 以下のように、structだけでなく、任意の型にも定義できる
// type MyFloat float64
// func (mf MyFloat) Abs() float64 {}

func main() {
  v := Vertex{3, 4}
  fmt.Println(v.Abs())
}
```

### Rewrite methods

- 匿名フィールドを利用して、継承したstructに対して、個別のメソッドを定義したい場合はレシーバ引数に継承先のtypeの型を持たせたメソッドを別途定義する

```go
type Human struct {
    name string
    age int
    phone string
}

type Student struct {
    Human //匿名フィールド
    school string
}

type Employee struct {
    Human //匿名フィールド
    company string
}

//Humanでmethodを定義
func (h *Human) SayHi() {
    fmt.Printf("Hi, I am %s you can call me on %s\n", h.name, h.phone)
}

//EmployeeのmethodでHumanのmethodを書き直す。
func (e *Employee) SayHi() {
    fmt.Printf("Hi, I am %s, I work at %s. Call me on %s\n", e.name,
        e.company, e.phone) //Yes you can split into 2 lines here.
}
```

### Pointer receivers

- ポインタレシーバでもメソッドを定義できる
- ある型`T`のポインタレシーバ`*T`を型の部分に書く（ただし、`T`は`*int`のようにポインタ自身をとることはできない）
- ポインタレシーバを持つメソッドは、レシーバが指す変数を変更できるため、ポインタレシーバが一般的に使われる
  - レシーバ自身を更新することが多いため。変数レシーバでは、変数が持つプロパティを更新することができない。
  - `func (*v Vertex) Scale(f float64)`であれば、`v.Scale(float)`は`Scale(&v, float)`と同じ挙動になる
  - `Scale(&v, float)`のように、参照渡しでなければ、値は変更できないということ
  - `v.Scale(float)`は`(&v).Scale(float)`であるが、Golangでは、`v.Scale(float)`と書いても同様の解釈ができる
- ポインタレシーバが一般的である理由
  - メソッドがレシーバが指す先の変数を変更できるから
  - メソッドの呼び出しごとに変数のコピーを避けるため。参照渡しであれば値をコピーしなくていい
  - __全てのメソッドは、変数レシーバまたはポインタレシーバのどちらかで統一すること__

```go
type Vertex struct {
  X, Y float64
}
// v Vertexの場合、X, Yは更新されない
func (*v Vertex) Scale(f float64) {
  v.X = v.X * f
  v.Y = v.Y * f
}
```

- メソッドが変数レシーバである場合、呼び出し時に、変数、またはポインタのいずれかのレシーバとしてとることができる

```go
type Vertex struct {
  X, Y float64
}

func (v Vertex) Abs() float64 {
  return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
var v Vertex
fmt.Println(v.Abs()) // OK
p := &v
fmt.Println(p.Abs()) // OK 変数レシーバであれば、ポインタからも呼び出せる。この場合、`(*p).Abs()`として解釈される
```

## Interfaces

- Goのinterfaceはメソッドのシグニチャの集まりで定義する
- 型にメソッドを実装していくことによって、インタフェースを実装する。（Javaのように`implements`は必要ない）

```go
type Abser interface {
  Abs() float64
}

type Vertex struct {
  X, Y float64
}

func (v *Vertex) Abs() float64 {
  return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
  var a Abser
  v := Vertex{3, 4}
  a = &v // a *Vertex implements Abser

  fmt.Println(a.Abs())
}
```

- interface自体の中にある具体的な値が`nil`の場合、メソッドは、`nil`をレシーバーとして呼び出される。
  - 一般的な言語では、nullポインタ例外が出るが、Golangでは、`nil`をレシーバーとして呼び出されても適切に処理するメソッドを用意するのが一般的
  - 呼び出す具体的なメソッドを示す型がインタフェースのタプル内に存在しない場合は、メソッドを呼び出すとランタイムエラーになる。（タプル内のメソッドを定義しない場合にランタイムエラーになる）

```go
type I interface {
  M()
}

type T struct {
  S string
}

func (t *T) M() {
  if t == nil {
    fmt.Println("<nil>")
    return
  }
  fmt.Println(t.S)
}

func main() {
  var i I

  var t *T
  i = t
  i.M()
}
```

### The empty interface

- `interface{}`で、空のインタフェースを定義できる。
- 空のインタフェースは、任意の型の値を保持できる
- 未知の型を扱うコードで利用される。例えば、`fmt.Print`など

```go
var i interface{}
i = 42
i = "hello"
```

#### Type assertions

- 型アサーション（type assertion）は、インタフーフェースの値の基になる具体的な値を利用する手段を提供
- `t := i.(T)`で利用できる。（`i`: interfaceの値, `T`: 任意の型）
- `i`が型`T`でない場合、panicを引き起こす。
- `s, ok := i.(T)`と使う場合、`i`に`T`型が含まれている場合、`ok`は`true`、そうでない場合は`false`を含む。
  - `s`は`ok == true`の場合、`i.(T)`の値を含む

#### Type switches

- 型switchはいくつかの型アサーションを直列に使用できる構造
- この構文を使う時のみ、`i.(type)`を使う（普段は使えない）
- `i`が`T`を保持していれば、`case T`の中に入って操作する
- ここで、`v`は`i`と同じ値でインターフェース型

```go
switch v := i.(type) {
  case T:
    // ~~~
  case S:
    // ~~~
  default:
    // no match;
}
```

### Stringers

- `fmt`パッケージに定義されているinterface

```go
type Stringer interface {
  String() string
}
```

- 任意の変数を文字列として出力するために使う。
  - 例えば、`fmt.Println`の引数に変数が渡されたときに、出力形式を変更するために使う

```go
type Person struct {
  Name string
  Age  int
}

func (p Person) String() string {
  return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}

func main() {
  a := Person{"Arthur Dent", 42}
  z := Person{"Zaphod Beeblebrox", 9001}
  // String() stringが定義していない場合 : {Zaphod Beeblebrox, 9001}
  // String() stringを定義している場合   : Zaphod Beeblebrox (9001 years)
  fmt.Println(a, z)
}
```

## Errors

- Golangはエラーの状態を`error`値で表現する
- `error`型は`fmt.Stringer`に似た組み込みのインタフェース

```go
type error interface {
  Error() string
}
```

- 関数のエラーは呼び出し元がエラーが`nil`かどうか確認することで取り扱う

```go
// エラーハンドリングの例。第二引数がerror値を受け取る
i, err := strconv.Atoi("42")
if err != nil {
  // ~~~
}
```

- `error`型を返却する関数の定義例

```go
type ErrNegativeSqrt float64

func (err ErrNegativeSqrt) Error() string {
  return fmt.Sprintf("cannot Sqrt negative number: %d", err)
}

func Sqrt(x float64) (float64, error) {
  if x < 0 {
    return 0, ErrNegativeSqrt(x)
  }
  return math.Sqrt(x), nil
}

func main() {
  fmt.Println(Sqrt(2))
  fmt.Println(Sqrt(-2))
}
```

## Readers

- `io`パッケージに規定されているデータストリームを読むことを表現するインターフェース `io.Reader`
- `Read`はデータを与えられたバイトスライスへ入れて、入れたバイトサイズとエラーの値を返却する。
- ストリームの終端は、`io.EOF`のエラーで返却する

```go
r := strings.NewReader("Hello, Reader!")

b := make([]byte, 8)
for {
  // n: 読み取った値, err: エラーの値
  n, err := r.Read(b)
  fmt.Printf("n = %v err = %v b = %v\n", n, err, b)
  fmt.Printf("b[:n] = %q\n", b[:n])
  // err == io.EOFのときに終端
  if err == io.EOF {
    break
  }
}
```

- `io.Reader`インタフェースは`Read`メソッドを持つため、自分であるインタフェースに対する`Read`メソッドを定義することもできる

```go
type MyReader struct{}
func (reader MyReader) Read(b []byte) (int, error) {
  for i := range b {
    b[i] = 'A'
  }
  return len(b), nil
}
```

## Images

- `image`パッケージは、以下の`Image`インタフェースを定義している

```go
    ColorModel() color.Model
    Bounds() Rectangle  // image.Rectangle
    At(x, y int) color.Color
```

## Goroutines

- `goroutine`（ゴルーチン）は、Goのランタイムに管理される軽量なスレッド
- `go f(x, y, z)`と書けば、goroutineが実行される
  - `x`, `y`, `z`の評価は、実行元のgoroutineで実行され、`f`の実行は、新しいgoroutineで実行される

```go
func say(s string) {
  for i := 0; i < 5; i++ {
    time.Sleep(100 * time.Millisecond)
    fmt.Println(s)
  }
}

func main() {
  go say("world")
}
```

### Channels

- `Channel`（チャネル）型は、チャネルオペレータの`<-`を用いて、値の送受信ができる通り道

```go
ch <- v    // v をチャネル ch へ送信する
v := <-ch  // ch から受信した変数を v へ割り当てる
// 利用する前の生成方法
ch := make(chan int)
```

- goroutineを用いて並行処理しているときに、あるgoroutineから別のgoroutineへ値を渡すために利用できる

```go
func sum(s []int, c chan int) {
  sum := 0
  for _, v := range s {
    sum += v
  }
  c <- sum // send sum to c
}

func main() {
  s := []int{7, 2, 8, -9, 4, 0}

  c := make(chan int)
  go sum(s[:len(s)/2], c)
  go sum(s[len(s)/2:], c)
  x, y := <-c, <-c // receive from c

  fmt.Println(x, y, x+y)
}
```

#### Buffered Channels

- チャネルは`buffer`（バッファ）として使える。
- `ch := make(chan int, 100)`のように、`make`の第二引数にバッファの長さを入れることで、バッファを持つチャネルの初期化が可能

#### Range and Close

- 送り手は、これ以上送信する値がないことを示すため、`close(チャネルを表す変数)`によってチャネルを閉じることができる
- 受け手は`v, ok := <-ch`を使い、`ok`が`false`である場合、チャンネルが閉じているということを確認できる
- 送り手のチャネルだけをcloseすること。受け手をcloseするとエラーが怒る

```go
func fibonacci(n int, c chan int) {
  x, y := 0, 1
  for i := 0; i < n; i++ {
    c <- x
    x, y = y, x+y
  }
  close(c)
}

func main() {
  c := make(chan int, 10)
  go fibonacci(cap(c), c)
  for i := range c {
    fmt.Println(i)
  }
}
```

### Select

- `select`はgoroutineを複数の通信操作で持たせるためのもの
- `select`の例
  - どの`case`も準備できていない場合、`switch`文と同様に、`default`内の処理を実行することができる

```go
// 以下のプログラムだと、mainパッケージの中のfor文で、チャネルの受信を待ち、
// fibonacci関数上で、チャネルが送信されたら、チャネルの内容を出力する
// 上記のループが繰り返され、fibonacciは、quitチャネルの受信待ちになる
// main中のforの中で、quitチャネルに値が送信され、fibonacciの処理を終了する
func fibonacci(c, quit chan int) {
  x, y := 0, 1
  for {
    select {
    case c <- x:
      x, y = y, x+y
    case <-quit:
      fmt.Println("quit")
      return
    // default:
    // ~~~~
    }
  }
}

func main() {
  c := make(chan int)
  quit := make(chan int)
  go func() {
    for i := 0; i < 10; i++ {
      fmt.Println(<-c)
    }
    quit <- 0
  }()
  fibonacci(c, quit)
}
```

### sync.Mutex

- goroutine間の通信が必要ない場合、`sync.Mutex`と`Lock`、`Unlock`によって、排他制御を実現し、一度に1つのgoroutineだけが変数にアクセスできるようにする
- 処理を`mu.Lock()`と`mu.Unlock()`で囲むだけでいい。（`mu`は`sync.Mutex`型の変数）
  - `defer`を使って、`mutex`が`Unlock`されることを保証するようにしてもいい

* * *

## リンク集

- [Go Web プログラミング](https://astaxie.gitbooks.io/build-web-application-with-golang/content/ja/)
- [Go Wiki](https://github.com/golang/go/wiki)
- [公式](http://golang.org/)
