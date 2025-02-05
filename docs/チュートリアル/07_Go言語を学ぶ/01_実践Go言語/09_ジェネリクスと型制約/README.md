[戻る](../README.md)

# ジェネリクスと型制約

ジェネリクスは、型安全性を保ちながらコードの再利用性を高めるための仕組みです。  
ここでは、型パラメータ、制約、そしてジェネリクスを使った関数や型の定義方法について説明します。

## 型パラメータと制約

ジェネリクスの中心となるのは型パラメータです。  
型パラメータは、関数や型定義の角括弧 `[]` 内に記述され、呼び出し側で具体的な型を決定します。  

例えば、以下の例では型パラメータ `T` を定義して、引数の型と戻り値の型として使用しています。  
`any` の部分では型パラメータ `T` に対する制約（constraints）を指定しています。  
`any` は「すべての型」を表す制約なので、`DoSomething` 関数の利用者はあらゆる型の値を引数に指定できます。

```go
func DoSomething[T any](v T) T {
    return v
}

func main() {
    DoSomething(100)
    DoSomething(100.1)
    DoSomething([]string{"100"})
}
```

次の例では、型パラメータ `T` の制約に `int | string` を指定しています。  
この制約では `T` 型が `int` または `string` であることを表しています。  
`DoSomething` 関数の内部では、`T` 型の値に対して `int` と `string` に共通する操作（`+`、`==`, `>`, `<` など）を記述できます。  
`DoSomething` 関数の利用者は `int` または `string` の値を引数に指定できます。

```go
func DoSomething[T int | string](v T) T {
    return v + v
}

func main() {
    DoSomething(100)    // 200 が返る
    DoSomething("100")  // "100100" が返る
}
```

## 複雑な制約の定義

### `interface` の利用

`interface` を用いることで、制約に名前を付け再利用できるようになります。  
例えば、次の `Number` 型は、`int | float64` の制約を意味します。  
作成した `Number` 型は型パラメータの制約として利用できます。

```go
type Number interface {
    int | float64
}

func DoSomething[T Number](v T) T {
    return v + v
}
```

### Underlying type 制約

`~` を使うことで基となる型 (`underlying type`) に対する制約をつくることができます。  
例えば、`~` を付けない `int` という制約は `int` のみを受け入れますが、`~int` という制約であれば `int` を基にしたあらゆる型を受け入れられます。

次の例で確認してください。

```go
// int を基にした Int という型
type Int int

// 型パラメータ T は int のみ受け入れる
func DoSomething1[T int](v T) T {
    return v + v
}

// 型パラメータ T は int を基にした型を受け入れる
func DoSomething2[T ~int](v T) T {
    return v + v
}

func main() {
    DoSomething1(Int(10)) // Int 型は int ではないためコンパイルエラー
    DoSomething2(Int(10)) // Int 型は int を基にした型であるため OK
}
```

Underlying type 制約をうまく利用することで、様々なメソッドを持つ派生型にも柔軟に対応できます。

### メソッドを持つ制約

制約にはメソッドを含めることもできます。  
次の例は `~string` かつ `String() string` メソッドを持つ制約です。

```go
type Stringer interface {
    ~string
    String() string
}
```

これまでは `|` により複数の制約のうちいずれかを満たす（OR）制約を作成してきましたが、  
メソッドを持つ制約は、他の制約を満たし、かつ、特定のメソッドを持つ（AND）制約のみ作成できます。

制約の記述は `|` で区切ることで OR の意味を、改行または `;` で区切ることで AND の意味を持ちます。  
ただし、`interface { string; int }` のような制約は現実的には満たすことができないため意味がありません。  
また、メソッドを持つ制約を `|` で利用することはできません。

次の例で確認してください。

```go
// 「String() string」メソッドを持つ制約として利用可能
type C1 interface { String() string }

// コンパイルエラー
// C1 はメソッドを持つ制約なので「|」を利用できない
type C2 interface { ~string | C1 }

// コンパイルは通るが、string かつ int の値は存在しない
type C3 interface { 
    string
    int
}

// 基底型が string であり、かつ、「String() string」メソッドを持つ制約として利用可能
type C4 interface {
    ~string
    C1
}
```

## 定義済みの制約

標準ライブラリでは次の制約が提供されています。  
また、試験的なパッケージとして `golang.org/x/exp/constraints` からもいくつかの制約が提供されています。

| パッケージ                     | 名前         | 概要                                                            |
| ------------------------------ | ------------ | --------------------------------------------------------------- |
| 組み込み                       | `any`        | すべての型を許可する制約（`interface {}` のエイリアス）         |
| 組み込み                       | `comparable` | `==` 演算子で比較可能な型を許可する制約                         |
| `cmp`                          | `Ordered`    | `>`, `<`, `>=`, `<=` 演算子で大小の比較が可能な型を許可する制約 |
| `golang.org/x/exp/constraints` | `Complex`    | 任意の複素数値型を許可する制約                                  |
| `golang.org/x/exp/constraints` | `Float`      | 任意の浮動小数点型を許可する制約                                |
| `golang.org/x/exp/constraints` | `Integer`    | 任意の整数型を許可する制約                                      |
| `golang.org/x/exp/constraints` | `Ordered`    | `>`, `<`, `>=`, `<=` 演算子で大小の比較が可能な型を許可する制約 |
| `golang.org/x/exp/constraints` | `Signed`     | 任意の任意の符号付き整数型を許可する制約                        |
| `golang.org/x/exp/constraints` | `Unsigned`   | 任意の符号なし整数型を許可する制約                              |

----
[前へ](../08_関数にメソッドを追加する/README.md) | [次へ](../10_イテレータの利用と実装/README.md)
