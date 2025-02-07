[戻る](../README.md)

# 関数にメソッドを追加する

Go 言語ではさまざまなインターフェースを実装しますが、インターフェースを実装する手段は構造体だけではありません。  
関数自体に型を定義し、その型にメソッドを追加することでインターフェースを満たすことができます。  
ここではその方法と実装例を紹介します。

## 関数型にメソッドを追加する

通常、構造体にメソッドを追加してインターフェースを実装しますが、関数型にもメソッドを定義できます。

```go
package main

import (
    "fmt"
    "strings"
)

// Processor インターフェース
type Processor interface {
    Process(string) string
}

type ProcessorFunc func(string) string

// ProcessorFunc にメソッドを定義し、Processor インターフェースを満たす
func (h ProcessorFunc) Process(input string) string {
    return h(input)
}

func process(p Processor, input string) {
    fmt.Println(p.Process(input))
}

func main() {
    // ProcessorFunc 型の関数を Processor インターフェースとして利用
    toUpper := ProcessorFunc(func(s string) string {
        return strings.ToUpper(s)
    })

    process(toUpper, "hello") // HELLO
}
```

### 利点

- インターフェースを満たすために、新たな構造体を定義する必要がない
- シンプルな関数をそのままインターフェースに適合させられる
- 柔軟に関数の動作を差し替えられる

## 参考

- [Effective Go - Methods](https://go.dev/doc/effective_go#methods)

----
[前へ](../07_関数を受け取る関数、関数を返す関数（高階関数）/README.md) | [次へ](../09_ジェネリクスと型制約/README.md)
