[戻る](../README.md) | [NotebookLM を開く](https://notebooklm.google.com/notebook/72ccb774-4924-45b5-9241-c5b183370e0e)

# パッケージと可視性

Go 言語では、パッケージはコードの整理と可視性の管理を行う重要な単位です。  
適切にパッケージを分割し、可視性を制御することで、責務が分離され、再利用性や可読性が向上します。

## パッケージの基本

Go 言語では、`package` キーワードを使ってパッケージを定義します。

```go
package mypackage

import "fmt"

func PrintMessage() {
    fmt.Println("Hello from mypackage")
}
```

**パッケージ名はディレクトリ名と一致させる** のが一般的です。

## 可視性のルール

Go 言語では識別子（関数、変数、構造体など）を大文字ではじめることで、その識別子をパッケージ外へ公開（エクスポート）できます。  
一方、小文字ではじめるとパッケージ外へは非公開となり、そのパッケージ内でのみ利用可能となります。

```go
package mathutil

// 公開される関数
func Add(a, b int) int {
    return a + b
}

// パッケージ内でのみ使われる関数
func subtract(a, b int) int {
    return a - b
}
```

この `mathutil` パッケージの `Add` 関数は外部から `mathutil.Add(2, 3)` のように呼び出せますが、 `subtract` 関数は `mathutil` パッケージの外からは使用できません。

### 何を公開（エクスポート）するかでパッケージの責務を表現する

公開（エクスポート）する識別子はそのパッケージの責務をよく表した必要最低限のものに絞りましょう。  
不用意になんでも公開（エクスポート）してしまうと、パッケージの責務が読み取れなくなってしまいます。

## パッケージの分割

パッケージを分割するときは、単一責任の原則（SRP: Single Responsibility Principle）を意識することが重要です。  
1つのパッケージが複数の責務を持つと、再利用性や保守性が低下します。

### 責務の分離ができていない例

```go
package app

import "fmt"

func Authenticate(username, password string) bool {
    return username == "admin" && password == "password"
}

func SendEmail(to, subject, body string) {
    fmt.Println("Sending email to:", to)
}
```

`app` パッケージが認証 (`Authenticate`) とメール送信 (`SendEmail`) の両方を担当しており、責務が分離されていません。

### 責務を分離した例

```go
package auth

func Authenticate(username, password string) bool {
    return username == "admin" && password == "password"
}
```

```go
package email

import "fmt"

func Send(to, subject, body string) {
    fmt.Println("Sending email to:", to)
}
```

このように `auth` パッケージと `email` パッケージに分けることで、それぞれが1つの責務に集中し、コードの管理がしやすくなります。

### パッケージ名と可読性

パッケージ名はコードの可読性に大きく影響します。  
さきほどの例では、`app.SendEmail` と呼び出していた関数が、責務をうまく分割しパッケージ分けたことにより `email.Send` と呼び出すことができるようになっています。  
このとき、修正前の `app.` にはあまり意味がありません。そのために `SendEmail` のように関数名で説明を補う必要が出てきています。  
一方、修正後の `email.` は、それだけで Email であることを充分に説明できています。その結果、関数名も `Send` とシンプルなものにできました。

このように、パッケージが単一責任の原則に則り責務が明らかになっている場合、適切なパッケージ名をつけることで、パッケージ名をコードの説明として効果的に利用できるようになります。  
逆に、複数の責務をもった `util` のようなパッケージ名は、コードから読み取れる情報量が増えないため、可読性が落ちる要因になります。

----
[次へ](../02_構造体とメソッド、New関数/README.md)
