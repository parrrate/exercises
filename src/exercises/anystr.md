# `AnyStr`?

Create something similar to [Python's `typing.AnyStr`].

[Python's `typing.AnyStr`]: https://docs.python.org/3/library/typing.html#typing.AnyStr

Example of what it may allow (solution isn't required to pass these tests, but should):
```rust
# pub trait Equivalent {
# type T;
# fn st_ref(&self) -> &Self::T;
# fn st_mut(&mut self) -> &mut Self::T;
# fn st_move(self) -> Self::T;
# fn ts_ref(t: &Self::T) -> &Self;
# fn ts_mut(t: &mut Self::T) -> &mut Self;
# fn ts_move(t: Self::T) -> Self;
# }
# pub trait Accepts<T> {
# type Output;
# type E;
# fn accept(self) -> Self::Output where Self::E: Equivalent<T = T>;
# }
# pub trait AnyStr{fn provide<A:Accepts<String,E=Self,Output=Output>+Accepts<Vec<u8>,E=Self,Output=Output>,Output>(accepts:A)->Output;}
# impl<T> Equivalent for T {
# type T = T;
# fn st_ref(&self) -> &Self::T { self }
# fn st_mut(&mut self) -> &mut Self::T { self }
# fn st_move(self) -> Self::T { self }
# fn ts_ref(t: &Self::T) -> &Self { t }
# fn ts_mut(t: &mut Self::T) -> &mut Self { t }
# fn ts_move(t: Self::T) -> Self { t }
#  }
# impl AnyStr for String {
# fn provide<A:Accepts<String,E=Self,Output=Output>+Accepts<Vec<u8>,E=Self,Output=Output>,Output>(accepts:A)->Output{<A as Accepts<String>>::accept(accepts)}
# }
# impl AnyStr for Vec<u8> {
# fn provide<A:Accepts<String,E=Self,Output=Output>+Accepts<Vec<u8>,E=Self,Output=Output>,Output>(accepts:A)->Output{<A as Accepts<Vec<u8>>>::accept(accepts)}
# }
# pub struct Concat<E>(E, E);
# impl<E: AnyStr> Accepts<String> for Concat<E> {
# type Output = E;
# type E = E;
# fn accept(self) -> Self::Output where E: Equivalent<T = String> { E::ts_move(self.0.st_move() + self.1.st_ref()) }
# }
# impl<E: AnyStr> Accepts<Vec<u8>> for Concat<E> {
# type Output = E;
# type E = E;
# fn accept(self) -> Self::Output where E: Equivalent<T = Vec<u8>> {
# let mut a: Vec<u8> = self.0.st_move();
# let mut b: Vec<u8> = self.1.st_move();
# a.append(&mut b);
# E::ts_move(a)
# }
# }
# pub fn concat<T: AnyStr>(a: T, b: T) -> T { T::provide(Concat(a, b)) }
assert_eq!(concat("ab".to_string(), "cd".to_string()), "abcd".to_string());
assert_eq!(concat(vec![2u8, 3u8], vec![5u8, 7u8]), vec![2u8, 3u8, 5u8, 7u8]);
```
