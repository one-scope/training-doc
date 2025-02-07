[戻る](../README.md)

# 構造体とメソッド、New関数

Go 言語では、構造体（`struct`）を使ってデータを表現し、メソッドを定義することでオブジェクト指向的な設計が可能になります。

## 構造体の基本

```go
type User struct {
    Name  string
    Email string
}
```

構造体は `struct` キーワードを使って定義し、フィールドを持つことができます。

## メソッドの定義

Go 言語では、構造体にメソッドを定義できます。

```go
type User struct {
    Name  string
    Email string
}

// メソッドの定義
func (u User) Greet() string {
    return "Hello, " + u.Name
}
```

### メソッド名

メソッド名は通常、動詞（＋名詞）のかたちでキャメルケースで命名します。  
プログラムでよく使われる一般的な英単語を使うように心掛けましょう。

Go 言語の場合、エラーが発生しない単純な型変換やフィールド値の取得の場合、そのままフィールド名や型名をメソッド名にすることもあります。  
たとえば、構造体の内容を `string` 型で取得する `String() string` メソッド、非公開のフィールド `name string` を取得する `Name() string` メソッドなどがあります。  
他の言語で `GetXXX` や `ToXXX` のように命名するところを、ただ `XXX` とするイメージです。  
繰り返しになりますが、このような命名をする場合はエラーが起こり得ない、副作用のない、単純な関数だけにしてください。  
そうでない関数の場合は、その性質をよく表す命名が必要です。

## ポインタレシーバーと値レシーバー

Go 言語のメソッドは、値レシーバーまたはポインタレシーバーのどちらかで定義できます。

- 値レシーバー (`func (u User) Method()`)  
  構造体のコピーを操作するため、元のデータは変更されません。
- ポインタレシーバー (`func (u *User) Method()`)  
  元のデータを変更する必要がある場合に使用します。

```go
type User struct {
    Name string
}

// 値レシーバ：オリジナルの構造体は変更されない
func (u User) Greet() string {
    return "Hello, " + u.Name
}

// ポインタレシーバ：オリジナルの構造体を変更できる
func (u *User) SetName(name string) {
    u.Name = name
}

func main() {
    user := User{Name: "Alice"}
    user.SetName("Bob")
    fmt.Println(user.Name) // "Bob"
}
```

一般的に、構造体が大きい場合やフィールドを変更する場合はポインタレシーバーを使用するのが推奨されます。

## New 関数の利用

Go 言語では、`New` プレフィックスを持つ関数は、その型のインスタンスを作成する慣習があります。  
`New` 関数を用意することで、構造体の作成ロジックを統一できます。

```go
func NewUser(name string, email string) *User {
    return &User{Name: name, Email: email}
}

func main() {
    user := NewUser("Bob", "bob@example.com")
}
```

### New 関数のアンチパターン

`New` 関数はシンプルに構造体の初期化のみを行うためのものとし、副作用が伴ったりエラーが発生するような複雑な処理を含めるべきではありません。  
より複雑な初期化が必要な場合は、明示的に `Create` や `Load` などの別の関数名を使用してください。

```go
func CreateUser(name, email string) (*User, error) {
    if name == "" || email == "" {
        return nil, errors.New("name and email are required")
    }
    return &User{Name: name, Email: email}, nil
}
```

## 参考

- [Effective Go - Structs](https://go.dev/doc/effective_go#structs)

----
[前へ](../01_パッケージと可視性/README.md) | [次へ](../03_インターフェースによる抽象化/README.md)
