# count
* unordered_set[meta header]
* std[meta namespace]
* unordered_set[meta class]
* function[meta id-type]
* cpp11[meta cpp]

```cpp
size_type count(const key_type& x) const;              // (1) C++11
size_type count(const key_type& x, size_t hash) const; // (2) C++20

template <class K>
size_type count(const K& k) const;                     // (3) C++20
template <class K>
size_type count(const K& k, size_t hash) const;        // (4) C++20
```
* size_t[link /reference/cstddef/size_t.md]

## 概要
キーを検索し、コンテナ内に見つかった要素の数を返す。`set` コンテナはキーの重複を許さないため、この関数は実際には要素が見つかったときに 1 を、そうでないときに 0 を返す。

- (1) : キー`x`を検索し、合致する要素数を取得する
- (2) : キー`x`を、事前計算したハッシュ値をつけて検索し、合致する要素数を取得する
- (3) : キー`k`を透過的に検索し、合致する要素数を取得する
- (4) : キー`k`を、事前計算したハッシュ値をつけて透過的に検索し、合致する要素数を取得する

(3)と(4)の透過的な検索は、`Hash::transparent_key_equal`が定義される場合に有効になる機能であり、例として`unordered_set<string> s;`に対して`s.count("key");`のように`string`型のキーを持つ連想コンテナの検索インタフェースに文字列リテラルを渡した際、`string`の一時オブジェクトが作られないようにできる。詳細は[`std::hash`](/reference/functional/hash.md)クラスのページを参照。


## 事前条件
- (2) : 値`hash`と、[`hash_function()`](hash_function.md)`(x)`の戻り値が等値であること
- (4) : 値`hash`と、[`hash_function()`](hash_function.md)`(k)`の戻り値が等値であること


## 戻り値
指定されたキーと同じ値のキーの要素が見つかったなら 1、そうでないなら 0を返す。

メンバ型 `size_type` は符号なし整数型である。


## 例外
投げない。


## 計算量
`x`と`k`を共通の変数`a`であるとして、

- 平均： O(`count(a)`)
- 最悪： [`size`](size.md) について線形時間


## 備考
- (3), (4) : このオーバーロードは、`Hash::transparent_key_equal`型が定義される場合にのみ、オーバーロード解決に参加する
- (2), (4) : これらのオーバーロードは、キーに対するハッシュ値を事前に計算しておくことで、何度も同じキーで検索する場合に高速になる


## 例
### 基本的な使い方
```cpp example
#include <iostream>
#include <unordered_set>
#include <algorithm>
#include <iterator>

int main()
{
  std::unordered_set<int> us{ 1, 3, 5, 7, 9, };

  std::copy(us.begin(), us.end(), std::ostream_iterator<int>(std::cout, ", "));
  std::cout << std::endl;

  auto c1 = us.count(5);
  std::cout << "count of 5:" << c1 << std::endl;

  auto c2 = us.count(8);
  std::cout << "count of 8:" << c2 << std::endl;
}
```
* count[color ff0000]
* us.begin()[link begin.md]
* us.end()[link end.md]

#### 出力
```
9, 7, 5, 3, 1,
count of 5:1
count of 8:0
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
  if (us.count(key, hash) > 0) {
    auto it = us.find(key, hash);
    std::cout << *it << std::endl;
  }
}
```
* count[color ff0000]
* us.hash_function()[link hash_function.md]
* us.find[link find.md]

#### 出力
```
Alice
```

## バージョン
### 言語
- C++11

### 処理系
- [Clang](/implementation.md#clang): -
- [Clang, C++11 mode](/implementation.md#clang): 3.1
- [GCC](/implementation.md#gcc): -
- [GCC, C++11 mode](/implementation.md#gcc): 4.7.0
- [ICC](/implementation.md#icc): ?
- [Visual C++](/implementation.md#visual_cpp): ?


## 関連項目

| 名前 | 説明 |
|-----------------------------------|--------------------------|
| [`find`](find.md)               | 指定したキーの位置を検索 |
| [`equal_range`](equal_range.md) | 指定したキーの範囲を取得 |


## 参照
- [LWG Issue 2304. Complexity of `count` in unordered associative containers](http://www.open-std.org/jtc1/sc22/wg21/docs/lwg-defects.html#2304)
- [P0919R3 Heterogeneous lookup for unordered containers](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/p0919r3.html)
- [P0920R2 Precalculated hash values in lookup](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p0920r2.html)
