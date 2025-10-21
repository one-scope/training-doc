[戻る](../README.md) | [NotebookLM を開く](https://notebooklm.google.com/notebook/72ccb774-4924-45b5-9241-c5b183370e0e)

# インターフェースによる抽象化

Go 言語のインターフェースを活用すると、具体的な実装に依存せずに柔軟なコードを記述できます。  
インターフェースを用いることで、異なる実装を簡単に差し替えたり、テストの容易さを向上させたりできます。

## インターフェースの基本

Go 言語のインターフェースでは明示的な継承（extends）や実装宣言（implements）を必要としません。  
インターフェースに定義されたメソッドを持っているのなら、その型はインターフェースを満たしているとみなされます。  
これは、「もし何かがアヒルのように歩き、アヒルのように鳴くならば、それはアヒルである」という「ダックタイピング（Duck Typing）」の概念に基づくものです。

次の例では、「面積を求めることができるもの」を「図形」として抽象化しています。  
最初に、面積を求める `Area() float64` メソッドを持つ型を図形 `Shape` インターフェースとして定義しています。  
そして、円を表す `Circle` 型と、四角形を表す `Rectangle` 型をそれぞれ定義しています。  
2つの型はどちらも面積を求める `Area() float64` メソッドを実装しているため、図形 `Shape` インターフェースを満たします。  
最後に登場する `PrintArea(s Shape)` 関数は図形 `Shape` インターフェースを受け取る関数ですが、  
円を表す `Circle` 型も、四角形を表す `Rectangle` 型も、図形 `Shape` インターフェースを満たしているため、  
`PrintArea(s Shape)` 関数の引数として渡すことができます。

```go
package main

import "fmt"

// 図形インターフェース
// ここでは「面積を求めることができるもの」を「図形」として定義した。
type Shape interface {
    // 面積を求めるメソッド
    Area() float64
}

// 円を表す構造体
type Circle struct {
    Radius float64
}

// 円の面積を求めるメソッド
func (c Circle) Area() float64 {
    return 3.14 * c.Radius * c.Radius
}

// 四角形を表す構造体
type Rectangle struct {
    Width, Height float64
}

// 四角形の面積を求めるメソッド
func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// あらゆる図形を受け取り、面積を表示する関数
func PrintArea(s Shape) {
    fmt.Println("Area:", s.Area())
}

func main() {
    c := Circle{Radius: 5}
    r := Rectangle{Width: 4, Height: 6}

    PrintArea(c) // Circle の面積を表示
    PrintArea(r) // Rectangle の面積を表示
}
```

### インターフェースの命名

インターフェースの名前はその役割をよく表すものにしましょう。  
Go 言語では名詞の他に、「～する人」というニュアンス（名詞化）で 動詞+`er` や 動詞+`or` がよく使われます。  
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

----
[前へ](../02_構造体とメソッド、New関数/README.md) | [次へ](../04_io.Readerとio.Writer/README.md)
