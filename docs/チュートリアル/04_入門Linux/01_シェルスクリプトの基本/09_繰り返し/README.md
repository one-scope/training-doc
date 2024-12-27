[戻る](../README.md)

# 繰り返し

## `for` ループ

シェルスクリプトでの `for` ループは次のように記述します。  
`リスト` はスペース区切りの数値や文字列です。

```bash
for 変数 in リスト; do
  # 繰り返し実行する処理
done
```

次の例では、`1` から `5` までの数字を順番に表示します。

```bash
for i in 1 2 3 4 5; do
  echo "数字: ${i}"
done
```

`seq` コマンドを使って数字のリストをつくることもできます。

```bash
for i in $(seq 1 5); do
  echo "数字: ${i}"
done
```

数字のリストをつくるにはブレース展開も利用できます。

```bash
for i in {1..5}; do
  echo "数字: ${i}"
done
```

ワイルドカードを使用してディレクトリ内のファイルに対して処理を実行することも可能です。

```bash
for filename in *.txt; do
  echo "テキストファイル: ${filename}"
done
```

----
[前へ](../08_ブレース展開/README.md) | [次へ](../10_シェルオプション/README.md)
