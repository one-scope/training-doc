[戻る](../README.md)

# 関数を受け取る関数、関数を返す関数（高階関数）

Go 言語では関数を引数として受け取ったり、関数を返す関数を定義することができます。  
このような関数のことを一般的に高階関数と呼びます。  
高階関数を利用することで、様々な設計に応用することが可能です。  
ここではその実装例を紹介します。

## 関数を引数に取る関数

以下の例では、整数のスライスに対して任意の処理を適用できる `apply` 関数を定義しています。

```go
package main

import "fmt"

func apply(numbers []int, operation func(int) int) []int {
    result := make([]int, len(numbers))
    for i, n := range numbers {
        result[i] = operation(n)
    }
    return result
}

func main() {
    nums := []int{1, 2, 3, 4, 5}
    double := func(n int) int { return n * 2 }
    fmt.Println(apply(nums, double)) // [2 4 6 8 10]
}
```

## 関数を返す関数

関数を返すことで、状態を持つ関数を作成できます。

```go
package main

import "fmt"

func multiplier(factor int) func(int) int {
    return func(n int) int {
        return n * factor
    }
}

func main() {
    double := multiplier(2)
    triple := multiplier(3)
    fmt.Println(double(5)) // 10
    fmt.Println(triple(5)) // 15
}
```

## 参考

- [Effective Go - Functions](https://go.dev/doc/effective_go#functions)

----
[前へ](../06_関数と関数リテラル、クロージャ/README.md) | [次へ](../08_関数にメソッドを追加する/README.md)
