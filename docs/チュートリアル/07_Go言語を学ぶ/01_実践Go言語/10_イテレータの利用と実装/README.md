[戻る](../README.md) | [NotebookLM を開く](https://notebooklm.google.com/notebook/72ccb774-4924-45b5-9241-c5b183370e0e)

# イテレータの利用と実装

ここでは Go 言語におけるイテレータの利用方法と実装方法について説明します。

## イテレータとは

イテレータ (iterator) とは、コンテナ（例：スライス、マップ、ツリーなど）内の要素を順番に「巡回（iterate）」するための仕組みです。  
これにより、利用者は「次の要素を取得する」という統一的な操作でデータにアクセスでき、内部のデータ構造に依存せずに済むため、コードの保守性や再利用性が向上します。  
また、イテレータを用いることで、フィルタリング、変換、ソートなどの共通処理を一貫した方法で実装でき、異なるコンテナ型でも同じアルゴリズムを適用可能になります。

## 標準ライブラリによるイテレータの標準化

標準ライブラリの `iter` パッケージで、インターフェースとしてイテレータが標準化されています。  
イテレータは次のように、関数として表現されています。

```go
package iter

type (
    // Seq は、要素 V を yield 関数に渡すシーケンスを表す。
    Seq[V any] func(yield func(V) bool)

    // Seq2 は、キー K と値 V のペアを yield 関数に渡すシーケンスを表す。
    Seq2[K, V any] func(yield func(K, V) bool)
)
```

## イテレータの利用

### for range での利用

`iter.Seq` を実装したイテレータは、次のように for range で利用できます。

```go
// 仮にイテレータ（iter.Seq）を返す関数があるとき、
func All[V any]() iter.Seq[V]

// for range で次のように各要素を巡回できる。
for v := range All[int]() {
    // v に対する処理
}
```

同様に、`iter.Seq2` を実装したイテレータは、次のように for range で利用できます。

```go
// 仮にイテレータ（iter.Seq2）を返す関数があるとき、
func All[K, V any]() iter.Seq2[K, V]

// for range で次のように各要素を巡回できる。
for k, v := range All[int, string]() {
    // k, v に対する処理
}
```

このように、関数として表現されたイテレータを for range で利用できる機能のことを range over func といいます。

### 標準ライブラリを用いたイテレータの利用

標準ライブラリには、`iter.Seq` や `iter.Seq2` を扱う関数が多数用意されています。  
これらを組み合わせることでイテレータを用いた柔軟な操作が可能です。

#### slices パッケージ

| 関数                                                                    | 概要                                                                               |
| ----------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| `func All[Slice ~[]E, E any](s Slice) iter.Seq2[int, E]`                | スライスの各要素に対して、そのインデックスと要素を走査するイテレータを返す         |
| `func Backward[Slice ~[]E, E any](s Slice) iter.Seq2[int, E]`           | スライスを逆順に走査するイテレータを返す                                           |
| `func Values[Slice ~[]E, E any](s Slice) iter.Seq[E]`                   | スライスの各要素を走査するイテレータを返す                                         |
| `func AppendSeq[Slice ~[]E, E any](s Slice, seq iter.Seq[E]) Slice`     | 既存のスライスに対して、イテレータから取り出した要素を追加した新たなスライスを返す |
| `func Collect[E any](seq iter.Seq[E]) []E`                              | イテレータからすべての要素を取り出し、スライスとして返す                           |
| `func Sorted[E cmp.Ordered](seq iter.Seq[E]) []E`                       | イテレータのすべての要素を昇順にソートして、スライスとして返す                     |
| `func SortedFunc[E any](seq iter.Seq[E], cmp func(E, E) int) []E`       | イテレータのすべての要素を比較関数の順にソートして、スライスとして返す             |
| `func SortedStableFunc[E any](seq iter.Seq[E], cmp func(E, E) int) []E` | イテレータのすべての要素について安定ソートを実行し、スライスとして返す             |
| `func Chunk[Slice ~[]E, E any](s Slice, n int) iter.Seq[Slice]`         | スライスをn個ずつのチャンク（部分スライス）に分割するイテレータを返す              |

#### maps パッケージ

| 関数                                                                         | 概要                                                     |
| ---------------------------------------------------------------------------- | -------------------------------------------------------- |
| `func All[Map ~map[K]V, K comparable, V any](m Map) iter.Seq2[K, V]`         | マップのすべてのキーと値のペアを走査するイテレータを返す |
| `func Keys[Map ~map[K]V, K comparable, V any](m Map) iter.Seq[K]`            | キーのみを走査するイテレータを返す                       |
| `func Values[Map ~map[K]V, K comparable, V any](m Map) iter.Seq[V]`          | 値のみを走査するイテレータを返す                         |
| `func Insert[Map ~map[K]V, K comparable, V any](m Map, seq iter.Seq2[K, V])` | イテレータから取り出したキーと値のペアをマップに挿入する |
| `func Collect[K comparable, V any](seq iter.Seq2[K, V]) map[K]V`             | キーと値のペアのイテレータから、新たなマップを生成する   |

#### strings パッケージ

| 関数                                                               | 概要                                                                   |
| ------------------------------------------------------------------ | ---------------------------------------------------------------------- |
| `func Lines(s string) iter.Seq[string]`                            | 改行で終了する行のイテレータを返す                                     |
| `func SplitSeq(s, sep string) iter.Seq[string]`                    | セパレータで分割された部分文字列に対するイテレータを返す               |
| `func SplitAfterSeq(s, sep string) iter.Seq[string]`               | セパレータの各インスタンスの後で分割された部分文字列のイテレータを返す |
| `func FieldsSeq(s string) iter.Seq[string]`                        | 空白文字で分割された部分文字列に対するイテレータを返す                 |
| `func FieldsFuncSeq(s string, f func(rune) bool) iter.Seq[string]` | 関数で分割された部分文字列に対するイテレータを返す                     |

#### bytes パッケージ

| 関数                                                               | 概要                                                                     |
| ------------------------------------------------------------------ | ------------------------------------------------------------------------ |
| `func Lines(s []byte) iter.Seq[[]byte]`                            | 改行で終了する行のイテレータを返す                                       |
| `func SplitSeq(s, sep []byte) iter.Seq[[]byte]`                    | セパレータで分割されたサブスライスに対するイテレータを返す               |
| `func SplitAfterSeq(s, sep []byte) iter.Seq[[]byte]`               | セパレータの各インスタンスの後で分割されたサブスライスのイテレータを返す |
| `func FieldsSeq(s []byte) iter.Seq[[]byte]`                        | 空白文字で分割されたサブスライスに対するイテレータを返す                 |
| `func FieldsFuncSeq(s []byte, f func(rune) bool) iter.Seq[[]byte]` | 関数で分割されたサブスライスに対するイテレータを返す                     |

## イテレータの実装

次の例では `E` 型の要素の集合を表す型 `Set` を定義しています。  
`Set` の `All` メソッドは `Set` 内のすべての要素を巡回（iterate）するイテレータを返します。

```go
type Set[E comparable] struct {
    m map[E]struct{}
}

// Set の全要素を yield するイテレータを返す。
func (s *Set[E]) All() iter.Seq[E] {
    // iter.Seq を満たす関数を無名関数で実装して返す。
    return func(yield func(E) bool) {
        for v := range s.m {
            // yield 関数の呼び出しにより、for range へ要素 v を送り込む。
            if !yield(v) {
                // for range が break や return などにより中断したとき、yield 関数は false を返す。
                // yield 関数が false を返したら、走査を中断して終了する。
                return
            }
        }
    }
}
```

`iter.Seq2` を実装する場合もほとんど同じですが、yield 関数が `func(K, V) bool` のように2つの引数を受け取ります。

----
[前へ](../09_ジェネリクスと型制約/README.md)
