[戻る](../README.md)

# `error` の判定と伝搬方法

Go 言語ではエラーハンドリングが重要な設計要素の一つです。  
ここでは、エラーの判定方法や、伝搬方法について説明します。

## `error` の判定

Go 言語のエラーハンドリングでは、エラーが発生しているかどうかをまず `nil` で判定します。  
そのうえで、具体的なエラーの内容や種類を特定したい場合は `errors.Is` を利用します。

### エラーがあるかどうかを判定する

関数呼び出し後、エラー変数が `nil` でない場合は何らかのエラーが発生しているとみなします。これは、以下のようにシンプルにチェックできます。

```go
result, err := someFunction()
if err != nil {
    // エラーが発生しているので、適切な処理を行う
    fmt.Println("Error:", err)
    return
}
```

この `nil` チェックは、エラーが発生したかどうかを迅速に判断するための基本中の基本です。

### エラーを特定し、エラーに応じた処理を実行する

エラーがあったとき、それが具体的にどのようなエラーであるか判定したいケースがあります。  
例えば、ファイル操作を行っていたとき発生したエラーが `os.ErrNotExist` であるならば、ファイルが存在しないことがわかるので、ファイルを作成する処理を行ってから再度処理を実行するなどの対処が可能です。  
他にも、ストリームを読み込んでいるときに発生したエラーが `io.EOF` であるならば、ストリームの終端に到達したことがわかるので、処理の目的次第ですが、エラーではなく正しく終了させることが可能です。

エラーを特定するには `errors.Is` を使います。  

```go
result, err := someFunction()
if err != nil {
    if errors.Is(err, os.ErrNotExist) {
        // ファイルが存在しないときの処理
        return
    }

    // その他のエラーの処理
    fmt.Println("Error:", err)
    return
}
```

エラーを特定するために `==` での比較を使用しないでください。  
Go 言語のエラーはラップすることで基底エラーを持つことができる構造になっています。  
例えば、`os.ErrNotExist` をラップして、メッセージに具体的なファイルパスを含んだ新しいエラーをつくることができます。この時、基底エラーは `os.ErrNotExist` ですが、エラーのインスタンスは `os.ErrNotExist` とは別物なので、`==` では比較できません。  
`errors.Is` はラップされたエラーを再帰的にアンラップしながら基底エラーを含めて判定してくれます。

## 標準ライブラリを利用した `error` の返しかた

### `errors.New(text string) error`

エラーメッセージからシンプルな `error` をつくります。

### `fmt.Errorf(format string, a ...any) error`

エラーメッセージに `fmt.Printf` のような書式指定を利用できます。  
`%w` を使うことで元のエラーをラップして、基底エラーをもった `error` をつくれます。  

### 利用例

- 伝搬すべきエラーが存在せず、利用者側でエラーを特定する必要もない場合

  ```go
  func DoSomething() error {
      return errors.New("failed to open file")
  }
  ```

- 関数内で発生したエラーをそのまま伝搬し、利用者側でエラーを特定できるようにする場合

  ```go
  func DoSomething() error {
      err := doSomething()
      return fmt.Errorf("failed to open file: %w", err)
  }
  ```

- パッケージ内で共通のエラー変数を定義し、利用者側でエラーを特定できるようにする場合

  ```go
  var ErrNotFound = errors.New("not found")
  
  func DoSomething() error {
      return ErrNotFound
  }
  ```

- パッケージ内で基底エラーとするエラー変数を定義し、利用者側でエラーを特定できるようにする場合

  ```go
  var ErrNotFound = errors.New("not found")
  
  func DoSomething() error {
      return fmt.Errorf("failed to open file: %w", ErrNotFound)
  }
  ```

関数内部で発生したエラーをそのまま返すことは避けましょう。  
そのエラーが実際にはどこで発生したエラーであるのかがわからなくなってしまうためです。  
少なくともパッケージ単位で発生元がわかるようにラップすることを推奨します。

## 参考

- [Effective Go - Errors](https://go.dev/doc/effective_go#errors)

----
[前へ](../04_io.Readerとio.Writer/README.md) | [次へ](../06_関数と関数リテラル、クロージャ/README.md)
