Make this compile.
- `A1` doesn't know about/depend on `A2`
- `A2` doesn't know about/depend on `A1`
- `A1` doesn't know about/depend on `A`
- `A2` doesn't know about/depend on `A`
- can't edit definitions of `A`, `test`, `tes1`, `test2`; can only add code outside of those items
```rust
trait A {
    fn a_ref(&self) -> u64;
    fn a_mut(&mut self) -> bool;
    fn a_move(self) -> Self;
}

fn test(x: impl A) {
    x.a_ref();
    let mut x = x.a_move();
    x.a_mut();
}

fn test1(x: impl A1) {
    test(x)
}

fn test2(x: impl A2) {
    test(x)
}
# use std::marker::PhantomData;
# trait Wrapper<T>: Sized {}
# #[repr(transparent)]
# struct Wrapped<T, V: ?Sized>(PhantomData<V>, T);
# trait AProxy {
# type T;
# fn ap_ref(t: &Self::T) -> u64;
# fn ap_mut(t: &mut Self::T) -> bool;
# fn ap_move(t: Self::T) -> Self::T;
# }
# trait AWrapper: Wrapper<Self::AWrapped> { type AWrapped; }
# impl<T: AWrapper> A for T where T::AWrapped: AProxy<T = T>,
# {
# fn a_ref(&self) -> u64 { <T::AWrapped as AProxy>::ap_ref(self) }
# fn a_mut(&mut self) -> bool { <T::AWrapped as AProxy>::ap_mut(self) }
# fn a_move(self) -> Self { <T::AWrapped as AProxy>::ap_move(self) }
# }
# trait A1: AWrapper<AWrapped = Aw1<Self>> {
# fn a1_ref(&self) -> u64;
# fn a1_mut(&mut self) -> bool;
# fn a1_move(self) -> Self;
# }
# trait A2: AWrapper<AWrapped = Aw2<Self>> {
# fn a2_ref(&self) -> u64;
# fn a2_mut(&mut self) -> bool;
# fn a2_move(self) -> Self;
# }
# struct Av1;
# struct Av2;
# type Aw1<T> = Wrapped<T, Av1>;
# type Aw2<T> = Wrapped<T, Av2>;
# impl<T: ?Sized + A1> AProxy for Aw1<T> {
# type T = T;
# fn ap_ref(t: &Self::T) -> u64 { t.a1_ref() }
# fn ap_mut(t: &mut Self::T) -> bool { t.a1_mut() }
# fn ap_move(t: Self::T) -> Self::T { t.a1_move() }
# }
# impl<T: ?Sized + A2> AProxy for Aw2<T> {
# type T = T;
# fn ap_ref(t: &Self::T) -> u64 { t.a2_ref() }
# fn ap_mut(t: &mut Self::T) -> bool { t.a2_mut() }
# fn ap_move(t: Self::T) -> Self::T { t.a2_move() }
# }
```
