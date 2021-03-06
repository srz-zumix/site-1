# parse
* chrono[meta header]
* std::chrono[meta namespace]
* function[meta id-type]
* cpp20[meta cpp]

```cpp
namespace std::chrono {
  template <class charT, class traits, class Alloc, class Parsable>
  unspecified
    parse(const std::basic_string<charT, traits, Alloc>& fmt,
          Parsable& tp);                                      // (1) C++20

  template <class charT, class traits, class Alloc, class Parsable>
  unspecified
    parse(const std::basic_string<charT, traits, Alloc>& fmt,
          Parsable& tp,
          std::basic_string<charT, traits, Alloc>& abbrev);   // (2) C++20

  template <class charT, class traits, class Alloc, class Parsable>
  unspecified
    parse(const std::basic_string<charT, traits, Alloc>& fmt,
          Parsable& tp,
          minutes& offset);                                   // (3) C++20

  template <class charT, class traits, class Alloc, class Parsable>
  unspecified
    parse(const std::basic_string<charT, traits, Alloc>& fmt,
          Parsable& tp,
          std::basic_string<charT, traits, Alloc>& abbrev,
          minutes& offset);                                   // (4) C++20
}
```
* unspecified[italic]

## 概要
日時文字列を解析する入力マニピュレータ。

- (1) タイムゾーン関係の入力を受け取らない単純な解析を行う
- (2) chronoオブジェクトと、タイムゾーンの略称を解析する
- (3) chronoオブジェクトと、UTCタイムゾーンからのオフセット時間を解析する
- (4) chronoオブジェクトと、タイムゾーンの略称、UTCタイムゾーンからのオフセット時間を解析する

`fmt`パラメータに指定できるフォーマット指定子は、以下である。`N`として10進整数を指定できる場合があり、それを指定することでより多くの文字数を読み込める。

| フォーマット指定子 | 説明 | 例 |
|--------------------|------|----|
| `%a` | ロケール依存の曜日の略称 (大文字・小文字を区別しない) | `"Sun"`<br/> 日本のロケールでは`"日"` |
| `%A` | `%a`と等価 | |
| `%b` | ロケール依存の月の略称 (大文字・小文字を区別しない) | `"Apr"`<br/> 日本のロケールでは`"4月"` |
| `%B` | `%b`と等価 | |
| `%c` | ロケール依存の日付・時間の表現。改良コマンド`%Ec`を指定すると、異なる表現になる | `"Fri Apr 24 17:14:44 2020"`<br/> 日本のロケールでは`"2020年04月24日 17時14分44秒"`<br/> `%Ec`では`"令和02年04月24日 17時14分44秒"` |
| `%C` | 世紀の10進数番号。改良コマンド`%NC` (Nは10進整数) によって読み込む最大の文字数を指定できる。Nを指定子ない場合、デフォルトは2。先頭の0は許可されるが要求はされない。改良コマンド`%EC`と`%OC`はロケール依存の異なる表現になる | `"21"`<br/> `%EC`では元号`"令和"` |

(書きかけ)


## テンプレートパラメータ制約
- (1) : `from_stream(`[`declval`](/reference/utility/declval.md)`<basic_istream<charT, traits>&>(), fmt.`[`c_str()`](/reference/string/basic_string/c_str.md)`, tp)`が妥当な式であること
- (2) : `from_stream(`[`declval`](/reference/utility/declval.md)`<basic_istream<charT, traits>&>(), fmt.`[`c_str()`](/reference/string/basic_string/c_str.md)`, tp,` [`addressof`](/reference/memory/addressof.md)`(abbrev))`が妥当な式であること
- (3) : 式`from_stream(`[`declval`](/reference/utility/declval.md)`<basic_istream<charT, traits>&>(), fmt.`[`c_str()`](/reference/string/basic_string/c_str.md)`, tp,` [`declval`](/reference/utility/declval.md)`<`[`basic_string`](/reference/string/basic_string.md)`<charT, traits, Alloc>*>(), &offset)`が評価されないオペランドとして扱われた場合に妥当な式であること
- (4) : `from_stream(`[`declval`](/reference/utility/declval.md)`<basic_istream<charT, traits>&>(), fmt.`[`c_str()`](/reference/string/basic_string/c_str.md)`, tp,` [`addressof`](/reference/memory/addressof.md)`(abbrev), &offset)`が妥当な式であること


## 効果
- (1) : このマニピュレータは、[`basic_istream`](/reference/istream/basic_istream.md)`<charT, traits>`型オブジェクト`is`に対して`from_stream(is, fmt.`[`c_str()`](/reference/string/basic_string/c_str.md)`, tp)`を呼び出す
- (2) : このマニピュレータは、[`basic_istream`](/reference/istream/basic_istream.md)`<charT, traits>`型オブジェクト`is`に対して`from_stream(is, fmt.`[`c_str()`](/reference/string/basic_string/c_str.md)`, tp,` [`addressof`](/reference/memory/addressof.md)`(abbrev))`を呼び出す
- (3) : このマニピュレータは、[`basic_istream`](/reference/istream/basic_istream.md)`<charT, traits>`型オブジェクト`is`に対して`from_stream(is, fmt.`[`c_str()`](/reference/string/basic_string/c_str.md)`, tp, static_cast<`[`basic_string`](/reference/string/basic_string.md)`<charT, traits, Alloc>*>(nullptr), &offset)`を呼び出す
- (4) : このマニピュレータは、[`basic_istream`](/reference/istream/basic_istream.md)`<charT, traits>`型オブジェクト`is`に対して`from_stream(is, fmt.`[`c_str()`](/reference/string/basic_string/c_str.md)`, tp,` [`addressof`](/reference/memory/addressof.md)`(abbrev), &offset)`を呼び出す

以下、オーバーロード全体について、

- これらの関数をADL (引数依存の名前探索、argument dependent lookup) で呼び出すために、`from_stream`を修飾せずに呼び出している
- これらの関数は書式化されていない入力関数として動作するが ([`std::boolalpha`](/reference/ios/boolalpha.md), [`std::quoted`](/reference/iomanip/quoted.md)などの影響を受けない)、後続で呼び出された[`std::basic_istream`](/reference/istream/basic_istream.md)`<>::`[`gcount()`](/reference/istream/basic_istream/gcount.md)の戻り値に未規定の影響がある
- 概要欄に示したフォーマット指定子に含まれておらず、スペースを除くフォーマット文字列のすべての文字は、ストリームから変更されずに解析される
- スペース文字は、入力ストリーム内の「ゼロ個以上のスペース文字」と合致する
- 解析する`tp`の型が、フォーマット指定子の情報を表現できない場合、`is.`[`setstate`](/reference/ios/basic_ios/setstate.md)`(`[`ios_base::failbit`](/reference/ios/ios_base/type-iostate.md)`)`が呼び出される
    - 例として、[`duration`](duration.md)は[`weekday`](weekday.md)を表現できない
    - ただし、フォーマット指定子が時刻を表すものである場合 (`"%H"`, `"%I"`, `"%p"`など)、[`duration`](duration.md)の特殊化は、その日の深夜0時からの経過時間だと見なして読み込む
- フォーマット文字列で指定されたいずれの解析にも失敗した場合、もしくは完全な`duration`、時間点、カレンダーデータ構造を指定するために十分な情報が解析されなかった場合、`is.`[`setstate`](/reference/ios/basic_ios/setstate.md)`(`[`ios_base::failbit`](/reference/ios/ios_base/type-iostate.md)`)`が呼び出される


## 戻り値
未規定


## 例
```cpp example
#include <iostream>
#include <chrono>

namespace chrono = std::chrono;

int main()
{
}
```

### 出力
```
```

## バージョン
### 言語
- C++20

### 処理系
- [Clang](/implementation.md#clang): (9.0時点で実装なし)
- [GCC](/implementation.md#gcc): (9.2時点で実装なし)
- [Visual C++](/implementation.md#visual_cpp): (2019 Update 3時点で実装なし)
