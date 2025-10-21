[戻る](../README.md) | [NotebookLM を開く](https://notebooklm.google.com/notebook/72ccb774-4924-45b5-9241-c5b183370e0e)

# 関数と関数リテラル、クロージャ

Go 言語では関数は第一級オブジェクトであり、変数や値と同じように扱うことができます。  
関数リテラル（無名関数）やクロージャを利用することで柔軟な設計が可能になります。

## 関数を変数に代入する

Go 言語では通常の関数や構造体のメソッドを変数に代入し、後から呼び出すことができます。

```go
package main

import "fmt"

// 通常の関数
func greet(name string) {
    fmt.Println("Hello,", name)
}

// 構造体とメソッド
type Printer struct{}

func (p Printer) PrintMessage(msg string) {
    fmt.Println(msg)
}

func main() {
    // 通常の関数を変数に代入
    f := greet
    f("Alice") // Hello, Alice

    // 構造体のメソッドを変数に代入
    p := Printer{}
    printFunc := p.PrintMessage
    printFunc("Hello, Go!") // Hello, Go!
}
```

## 関数リテラル

関数リテラル（無名関数）は、その場で関数を定義し即座に実行したり、変数に代入して利用できます。

```go
package main

import "fmt"

func main() {
    add := func(a, b int) int {
        return a + b
    }
    fmt.Println(add(3, 5)) // 8
}
```

## クロージャ

クロージャは外側のスコープの変数をキャプチャして利用する関数です。

```go
package main

import "fmt"

func main() {
    message := "Hello"
    sayHello := func() {
        fmt.Println(message)
    }
    sayHello() // Hello
    
    message = "Hi"
    sayHello() // Hi
}
```

この例では `message` という変数を `sayHello` 関数がキャプチャしており、`message` の値が変わると `sayHello` の出力も変わります。

----
[前へ](../05_errorの判定と伝搬方法/README.md) | [次へ](../07_関数を受け取る関数、関数を返す関数（高階関数）/README.md)
