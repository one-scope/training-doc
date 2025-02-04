[戻る](../README.md)

# インターフェースによる抽象化

Go 言語のインターフェースを活用すると、具体的な実装に依存せずに柔軟なコードを記述できます。  
インターフェースを用いることで、異なる実装を簡単に差し替えたり、テストの容易さを向上させたりできます。

## インターフェースの基本

Go 言語のインターフェースは、特定のメソッドの集合を定義する型です。

```go
package main

import "fmt"

// 何かしらの方法で、指定した内容の通知を行うインターフェース
//  Notify(string) メソッドを持つ任意の型を表す
type Notifier interface {
    Notify(string)
}

// Email での通知を行う、Notifier インターフェースを満たす構造体
type EmailNotifier struct{}

func (e EmailNotifier) Notify(message string) {
    fmt.Println("Sending email:", message)
}

// SMS での通知を行う、Notifier インターフェースを満たす構造体
type SMSNotifier struct{}

func (s SMSNotifier) Notify(message string) {
    fmt.Println("Sending SMS:", message)
}

// Notifier インターフェースを満たす型を使って、実際に通知を行う関数
func SendNotification(n Notifier, message string) {
    n.Notify(message)
}

func main() {
    // Email での通知を実行
    email := EmailNotifier{}
    SendNotification(email, "You have a new message!")

    // SMS での通知を実行
    sms := SMSNotifier{}
    SendNotification(sms, "Your verification code is 123456")
}
```

この例では、Email で通知を行う `EmailNotifier` と SMS で通知を行う `SMSNotifier` はどちらも `Notify(string)` メソッドを実装しているため、`Notifier` インターフェースを満たしています。  
`SendNotification` 関数の引数は `Notifier` インターフェースであるので、`Notifier` インターフェースを満たす `EmailNotifier` と `SMSNotifier` を受け取ることができます。  
このとき `SendNotification` 関数の実装では、具体的な通知方法を知る必要がありません。  
もし新しい通知方法を追加する場合でも、`SendNotification` 関数の変更は不要です。

### インターフェースを使用しない場合

インターフェースを使用しない場合、具体的な型ごとに関数を用意する必要があり、コードの柔軟性が失われます。

```go
func SendEmailNotification(e EmailNotifier, message string) {
    e.Notify(message)
}

func SendSMSNotification(s SMSNotifier, message string) {
    s.Notify(message)
}
```

### インターフェースの命名

インターフェースの名前はその役割をよく表すものにしましょう。  
Go 言語では「～する人」というニュアンス（名詞化）で 動詞+`er` や 動詞+`or` がよく使われます。  
他の言語では「～できる人」というニュアンス（形容詞化）で 動詞+`able` なども使われますが、Go 言語では 動詞+`er` が一般的です。  
また、他の言語ではインターフェースの接頭辞に `I` をつけるものもありますが、Go では推奨されていません。

## 定義済みの主なインターフェース

標準ライブラリで提供されている主なインターフェースです。

| パッケージ      | インターフェース    | 概要                                                                               |
| --------------- | ------------------- | ---------------------------------------------------------------------------------- |
| `io`            | `Reader`            | `Read([]byte) (int, error)` を持つ読み取りインターフェース                         |
| `io`            | `Writer`            | `Write([]byte) (int, error)` を持つ書き込みインターフェース                        |
| `io`            | `Closer`            | `Close() error` を持つリソースのクローズ用インターフェース                         |
| `io`            | `ReadCloser`        | `Reader` + `Closer` の組み合わせ                                                   |
| `io`            | `WriteCloser`       | `Writer` + `Closer` の組み合わせ                                                   |
| `io`            | `ReadWriter`        | `Reader` + `Writer` の組み合わせ                                                   |
| `io`            | `ReadWriteCloser`   | `Reader` + `Writer` + `Closer` の組み合わせ                                        |
| `io`            | `Seeker`            | `Seek(offset int64, whence int) (int64, error)` を持つファイル位置変更用           |
| `io`            | `ReaderAt`          | `ReadAt(p []byte, off int64) (n int, err error)` を持つランダムアクセス読み取り用  |
| `io`            | `WriterAt`          | `WriteAt(p []byte, off int64) (n int, err error)` を持つランダムアクセス書き込み用 |
| `io/fs`         | `FS`                | ファイルシステムの抽象化                                                           |
| `io/fs`         | `File`              | `io.Reader`, `io.Closer` などを組み合わせたファイル操作用                          |
| `fmt`           | `Stringer`          | `String() string` を持つ文字列表現の提供用                                         |
| `fmt`           | `GoStringer`        | `GoString() string` を持ち、Goコード表現を返す                                     |
| `error`         | `error`             | `Error() string` を持つエラー型                                                    |
| `context`       | `Context`           | 非同期処理のキャンセルやタイムアウトを管理する                                     |
| `net`           | `Conn`              | ネットワーク接続を表す (`ReadWriter`, `Close`, `LocalAddr`, `RemoteAddr`)          |
| `net`           | `Listener`          | ネットワークリスナー (`Accept`, `Close`)                                           |
| `net/http`      | `Handler`           | `ServeHTTP(ResponseWriter, *Request)` を持ち、HTTPリクエストを処理                 |
| `net/http`      | `ResponseWriter`    | HTTPレスポンスの書き込み用                                                         |
| `net/http`      | `RoundTripper`      | HTTPリクエストを送信しレスポンスを返す (`http.DefaultTransport` など)              |
| `net/http`      | `Flusher`           | HTTPストリーミング用 (`Flush()`)                                                   |
| `net/http`      | `Hijacker`          | HTTP接続を乗っ取る (`Hijack()`)                                                    |
| `sync`          | `Locker`            | `Lock()`, `Unlock()` を持つロック用                                                |
| `sync`          | `Cond`              | 条件変数 (`Wait`, `Signal`, `Broadcast`)                                           |
| `sort`          | `Interface`         | カスタムソート (`Len`, `Less`, `Swap`)                                             |
| `encoding`      | `BinaryMarshaler`   | `MarshalBinary() ([]byte, error)` を持つバイナリシリアライズ用                     |
| `encoding`      | `BinaryUnmarshaler` | `UnmarshalBinary([]byte) error` を持つバイナリデシリアライズ用                     |
| `encoding/json` | `Marshaler`         | `MarshalJSON() ([]byte, error)` を持つJSONシリアライズ用                           |
| `encoding/json` | `Unmarshaler`       | `UnmarshalJSON([]byte) error` を持つJSONデシリアライズ用                           |
| `database/sql`  | `Driver`            | SQLドライバ用                                                                      |
| `database/sql`  | `DB`                | データベース接続管理                                                               |
| `database/sql`  | `Tx`                | トランザクション管理                                                               |

## 参考

- [Effective Go - Interfaces](https://go.dev/doc/effective_go#interfaces)

----
[前へ](../02_構造体とメソッド、New関数/README.md) | [次へ](../04_io.Readerとio.Writer/README.md)
