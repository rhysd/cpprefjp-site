# uses_allocator
* memory[meta header]
* std[meta namespace]
* class template[meta id-type]
* cpp11[meta cpp]

```cpp
namespace std {
  template <class T, class Alloc>
  struct uses_allocator;

  template <class T, class Alloc>
  inline constexpr bool uses_allocator_v = uses_allocator<T, Alloc>::value; // C++17 から
}
```

## 概要
型 `T` が `Alloc` 型のアロケータオブジェクトを用いた uses-allocator 構築をする際に、実際にアロケータオブジェクトを使用するかを調べる。

このクラスが [`true_type`](/reference/type_traits/true_type.md) から派生する場合、以下のいずれかの構築が可能である：

- `T(allocator_arg, alloc, args...)` のように、第1引数に [`allocator_arg`](allocator_arg_t.md)、第2引数に `Alloc` 型のアロケータオブジェクト `alloc` をとる構築。
- `T(args..., alloc)` のように、最後の引数として `Alloc` 型のアロケータオブジェクト `alloc` をとる構築。

このクラスのデフォルト実装は、型 `T` が `public` なメンバ型 `allocator_type` を持っており、その型が `Alloc` から変換可能であれば [`true_type`](/reference/type_traits/true_type.md) から派生し、そうでなければ [`false_type`](/reference/type_traits/false_type.md) から派生する。  

型 `T` が `Alloc` から変換可能なメンバ型 `allocator_type` を持っていないが上記いずれかの構築が可能な場合は、[`true_type`](/reference/type_traits/true_type.md) から派生した本クラステンプレートの特殊化を提供することで、アロケータを用いた構築をサポートしていることを示すことが可能である。


## 備考
- 本型トレイツは、主にスコープアロケータモデルを採用するアロケータで使用されることを意図している。  
	標準ライブラリでは、[`scoped_allocator_adaptor`](/reference/scoped_allocator/scoped_allocator_adaptor.md)、[`polymorphic_allocator`](../memory_resource/polymorphic_allocator.md) クラステンプレートで使用されている。
- 標準ライブラリで提供されるいくつかの型は本型トレイツの特殊化を提供している。（[`tuple`](../tuple/tuple.md)、[`promise`](../future/promise.md)、各種コンテナアダプタ等）  
- [`pair`](../utility/pair.md) は [`tuple`](../tuple/tuple.md) と同列の機能と考えられるが、uses-allocator 構築をサポートしていない。このため、標準ライブラリで提供されるスコープアロケータモデルを採用したアロケータでは独自に [`pair`](../utility/pair.md) の各要素に対して uses-allocator 構築を適用している。  
	スコープアロケータモデルを採用したアロケータを自作する場合には、同様の対応を行う方が良いだろう。  
	なお、C++20 で [`pair`](../utility/pair.md) に対する特殊対応を含めた uses-allocator 構築サポートのためのユーティリティ関数が追加されたため、以前に比べて容易に uses-allocator 構築への対応が可能となった。


## 例
```cpp example
#include <iostream>
#include <memory>

template <class T, class Allocator = std::allocator<T>>
struct X {
  T x_;
  Allocator alloc_;
public:
  using allocator_type = Allocator;

  X(std::allocator_arg_t, Allocator alloc, T x)
    : alloc_(alloc), x_(x) {}
};

int main()
{
  const bool result = std::uses_allocator<X<int>, std::allocator<int>>::value;
  static_assert(result, "should be true");
}
```
* std::uses_allocator[color ff0000]
* std::allocator[link allocator.md]
* std::allocator_arg_t[link allocator_arg_t.md]

### 出力
```
```


## バージョン
### 言語
- C++11

### 処理系
- [Clang, C++11 mode](/implementation.md#clang): 3.0
- [GCC, C++11 mode](/implementation.md#gcc): 4.6.4
- [ICC](/implementation.md#icc): ??
- [Visual C++](/implementation.md#visual_cpp): 2012, 2013


## 関連項目
- [`scoped_allocator_adaptor`](../scoped_allocator/scoped_allocator_adaptor.md)
- [`polymorphic_allocator`](../memory_resource/polymorphic_allocator.md)
- [`tuple`](../tuple/tuple.md)
- [`promise`](../future/promise.md)
- [`uses_allocator_construction_args`](uses_allocator_construction_args.md)
- [`make_obj_using_allocator`](make_obj_using_allocator.md)
- [`uninitialized_construct_using_allocator`](uninitialized_construct_using_allocator.md)


## 参照
- [P0006R0 Adopt Type Traits Variable Templates from Library Fundamentals TS for C++17](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0006r0.html)
- [P0591R4 Utility functions to implement uses-allocator construction](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/p0591r4.pdf)
