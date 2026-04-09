# `AnyStr`?

Create something similar to [Python's `typing.AnyStr`].

[Python's `typing.AnyStr`]: https://docs.python.org/3/library/typing.html#typing.AnyStr

Example of what it may allow (solution isn't required to pass these tests, but should):

```rust
# pub trait Equivalent { type T; }
# pub trait Accepts<T> {
# type Output;
# type E;
# fn accept(self) -> Self::Output where Self::E: Equivalent<T = T>;
# }
# pub trait AnyStrRaw:Equivalent{fn provide<A:Accepts<String,E=Self,Output=Output>+Accepts<Vec<u8>,E=Self,Output=Output>,Output>(accepts:A)->Output;}
# impl<T> Equivalent for T { type T = T; }
# impl AnyStrRaw for String {
# fn provide<A:Accepts<String,E=Self,Output=Output>,Output>(accepts:A)->Output{<A as Accepts<String>>::accept(accepts)}
# }
# impl AnyStrRaw for Vec<u8> {
# fn provide<A:Accepts<Vec<u8>,E=Self,Output=Output>,Output>(accepts:A)->Output{<A as Accepts<Vec<u8>>>::accept(accepts)}
# }
# pub struct Concat<E: Equivalent>(E::T, E::T);
# impl<E: AnyStrRaw> Accepts<String> for Concat<E> {
# type Output = E::T;
# type E = E;
# fn accept(self) -> Self::Output where E: Equivalent<T = String> { self.0 + &self.1 }
# }
# impl<E: AnyStrRaw> Accepts<Vec<u8>> for Concat<E> {
# type Output = E::T;
# type E = E;
# fn accept(self) -> Self::Output where E: Equivalent<T = Vec<u8>> {
# let mut a = self.0;
# let mut b = self.1;
# a.append(&mut b);
# a
# }
# }
# pub trait AnyStr: AnyStrRaw<T = Self> {}
# impl<T: AnyStrRaw<T = T>> AnyStr for T {}
# pub fn concat<T: AnyStr>(a: T, b: T) -> T { T::provide(Concat(a, b)) }
assert_eq!(concat("ab".to_string(), "cd".to_string()), "abcd".to_string());
assert_eq!(concat(vec![2u8, 3u8], vec![5u8, 7u8]), vec![2u8, 3u8, 5u8, 7u8]);
```
