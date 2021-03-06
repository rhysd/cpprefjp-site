# equal_range
* unordered_set[meta header]
* std[meta namespace]
* unordered_set[meta class]
* function[meta id-type]
* cpp11[meta cpp]

```cpp
pair<iterator, iterator>
  equal_range(const key_type& x);                                    // (1) C++11
pair<const_iterator,
  const_iterator> equal_range(const key_type& x) const;              // (2) C++11

pair<iterator, iterator>
  equal_range(const key_type& x, size_t hash);                       // (3) C++20
pair<const_iterator,
  const_iterator> equal_range(const key_type& x, size_t hash) const; // (4) C++20

template <class K>
pair<iterator, iterator>
  equal_range(const K& k);                                           // (5) C++20
template <class K>
pair<const_iterator, const_iterator>
  equal_range(const K& k) const;                                     // (6) C++20

template <class K>
pair<iterator, iterator>
  equal_range(const K& k, size_t hash);                              // (7) C++20
template <class K>
pair<const_iterator, const_iterator>
  equal_range(const K& k, size_t hash) const;                        // (8) C++20
```
* pair[link /reference/utility/pair.md]
* size_t[link /reference/cstddef/size_t.md]

## 概要
コンテナ内の、指定されたキーと等しい全てのキー要素を含む範囲の境界を返す。`unordered_set` コンテナではキーの重複は無いため、この範囲は最大一つの要素を含む。 

もし指定されたキーがコンテナ内のどのキーともマッチしなかった場合、戻り値の範囲は長さ 0 になり、両方のイテレータは [`end`](end.md) を指す。

- (1) : 非`const`な`this`に対してキー`x`を検索し、合致する全ての要素を含む範囲を取得する
- (2) : `const`な`this`に対してキー`x`を検索し、合致する全ての要素を含む範囲を取得する
- (3) : 非`const`な`this`に対して、事前計算したハッシュ値をつけてキー`x`を検索し、合致する全ての要素を含む範囲を取得する
- (4) : `const`な`this`に対して、事前計算したハッシュ値をつけてキー`x`を検索し、合致する全ての要素を含む範囲を取得する
- (5) : 非`const`な`this`に対してキー`k`を透過的に検索し、合致する全ての要素を含む範囲を取得する
- (6) : `const`な`this`に対してキー`k`を透過的に検索し、合致する全ての要素を含む範囲を取得する
- (7) : 非`const`な`this`に対して、事前計算したハッシュ値をつけてキー`k`を透過的に検索し、合致する全ての要素を含む範囲を取得する
- (8) : `const`な`this`に対して、事前計算したハッシュ値をつけてキー`k`を透過的に検索し、合致する全ての要素を含む範囲を取得する

(5)、(6)、(7)、(8)の透過的な検索は、`Hash::transparent_key_equal`が定義される場合に有効になる機能であり、例として`unordered_set<string> s;`に対して`s.equal_range("key");`のように`string`型のキーを持つ連想コンテナの検索インタフェースに文字列リテラルを渡した際、`string`の一時オブジェクトが作られないようにできる。詳細は[`std::hash`](/reference/functional/hash.md)クラスのページを参照。


## 事前条件
- (3), (4) : 値`hash`と、[`hash_function()`](hash_function.md)`(x)`の戻り値が等値であること
- (7), (8) : 値`hash`と、[`hash_function()`](hash_function.md)`(k)`の戻り値が等値であること


## 戻り値
合致する要素の範囲を表す `pair` オブジェクトを返す。`pair::first` は 範囲の下境界にあたり、`pair::second` は 範囲の上境界にあたる。

そのような要素がない場合には、[`make_pair`](/reference/utility/make_pair.md)`(`[`end`](end.md)`(),` [`end`](end.md)`())`を返す。


## 計算量
- 平均： 定数時間
- 最悪： [`size`](size.md) について線形時間


## 備考
- [`unordered_set`](/reference/unordered_set/unordered_set.md) の場合には、等価なキーはたかだか1つであるため、[`find()`](find.md) ほど有用ではないと考えられる
- (5), (6), (7), (8) : これらのオーバーロードは、`Hash::transparent_key_equal`型が定義される場合にのみ、オーバーロード解決に参加する
- (3), (4), (7), (8) : これらのオーバーロードは、キーに対するハッシュ値を事前に計算しておくことで、何度も同じキーで検索する場合に高速になる


## 例
### 基本的な使い方
```cpp example
#include <iostream>
#include <string>
#include <unordered_set>
#include <algorithm>
#include <iterator>

template <class Iter>
void print_range(const std::string& label, Iter begin, Iter it1, Iter it2, std::ostream& os = std::cout)
{
  os << label << ": " << "[" << std::distance(begin, it1) << ", "  << std::distance(begin, it2) << ")" << std::endl;
}

int main()
{
  std::unordered_set<int> us{ 1, 3, 5, 7, 9, };

  std::copy(us.begin(), us.end(), std::ostream_iterator<int>(std::cout, ", "));
  std::cout << std::endl;

  auto p1 = us.equal_range(5);
  print_range("equal_range(5)", us.begin(), p1.first, p1.second);

  auto p2 = us.equal_range(8);
  print_range("equal_range(8)", us.begin(), p2.first, p2.second);
}
```
* equal_range[color ff0000]
* std::ostream[link /reference/ostream.md]
* us.begin()[link begin.md]
* us.end()[link end.md]
* first[link /reference/utility/pair.md]
* second[link /reference/utility/pair.md]

#### 出力
```
9, 7, 5, 3, 1,
equal_range(5): [2, 3)
equal_range(8): [5, 5)
```

### 事前計算しておいたハッシュ値を使用する (C++20)
```cpp example
#include <iostream>
#include <unordered_set>
#include <string>

int main()
{
  std::unordered_set<std::string> us = {"Alice", "Bob", "Carol"};

  // ハッシュ値を事前計算
  const char* key = "Alice";
  std::size_t hash = us.hash_function()(key);

  // 事前計算しておいたハッシュ値を、検索インタフェースにキーといっしょに渡すことで、
  // 内部でのハッシュ値計算を省略できる
  if (us.contains(key, hash)) {
    auto p = us.equal_range(key, hash);
    for (; p.first != p.second; ++p.first) {
      auto it = p.first;
      std::cout << *it << std::endl;
    }
  }
}
```
* equal_range[color ff0000]
* us.hash_function()[link hash_function.md]
* us.contains[link contains.md]

#### 出力
```
Alice
```

## バージョン
### 言語
- C++11

### 処理系
- [Clang](/implementation.md#clang): -
- [Clang, C++11 mode](/implementation.md#clang): 3.0, 3.1
- [GCC](/implementation.md#gcc): -
- [GCC, C++11 mode](/implementation.md#gcc): 4.4.7, 4.5.3, 4.6.3, 4.7.0
- [ICC](/implementation.md#icc): ?
- [Visual C++](/implementation.md#visual_cpp): ?


## 関連項目

| 名前 | 説明 |
|------|------|
| [`find`](find.md) | 指定したキーの位置を検索 |
| [`count`](count.md) | 指定したキーの要素数を取得 |


## 参照
- [P0919R3 Heterogeneous lookup for unordered containers](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/p0919r3.html)
- [P0920R2 Precalculated hash values in lookup](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p0920r2.html)
