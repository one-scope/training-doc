[戻る](../../../README.md)

# TypeScript + React を学ぶ

- [TypeScript + React を学ぶ](#typescript--react-を学ぶ)
  - [学習リソース](#学習リソース)
  - [制御コンポーネントと非制御コンポーネント](#制御コンポーネントと非制御コンポーネント)
  - [参考書籍・Web サイト](#参考書籍web-サイト)

## 学習リソース

| レベル | リンク                                                                                                                                 | 概要                                                 |
| ------ | -------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| 1      | [サバイバルTypeScript](https://typescriptbook.jp/)                                                                                     | TypeScript の基本機能を知ります。                    |
| 2      | [API Reference](https://ja.react.dev/reference/react)                                                                                  | React 公式のリファレンスです。適宜参照してください。 |
| 2      | [Learn React](https://ja.react.dev/learn)                                                                                              | React 公式のチュートリアルです。                     |
| 3      | [Documentation - Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)                                       | TypeScript の便利な組み込み型を知ります。            |
| 4      | [101 React Tips & Tricks For Beginners To Experts](https://dev.to/_ndeyefatoudiop/101-react-tips-tricks-for-beginners-to-experts-4m11) | よりよい React の書き方を学びます。                  |

## 制御コンポーネントと非制御コンポーネント

ユーザー操作を伴うすべてのコンポーネントは制御コンポーネントと非制御コンポーネントに分類できます。  
一般的に、どちらでもないコンポーネントはつくるべきではありません。

**制御コンポーネント**  
ユーザー操作の結果を親コンポーネントの state と直接的に連動させるコンポーネントです。

**非制御コンポーネント**  
ユーザー操作の結果をコンポーネント内部で保持するコンポーネントです。

|            | 制御コンポーネント                                   | 非制御コンポーネント                                                          |
| ---------- | ---------------------------------------------------- | ----------------------------------------------------------------------------- |
| props      | value と onChange を持つ<br/>defaultValue を持たない | defaultValue を持つ<br/>value を持たない                                      |
| 状態管理   | 親コンポーネント                                     | コンポーネント内部                                                            |
| 状態の更新 | 親コンポーネントはいつでも状態の更新が可能           | 親コンポーネントはマウント時に初期値のみ指定可能<br/>マウント後は更新できない |

## 参考書籍・Web サイト

- [Visual design rules you can safely follow every time](https://anthonyhobday.com/sideprojects/saferules/)
- [Notes on maintaining an internal React component library](https://gabe.pizza/notes-on-component-libraries/)
