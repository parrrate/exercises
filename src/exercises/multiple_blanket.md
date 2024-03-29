# Blanket, blanket, blanket

Make this compile with the following restrictions:

- **Can't edit definitions of `A`, `test`, `test1`, `test2`; can only add code outside of those items.**
- **Some types are `A1` but not `A2`.**
- **Some types are `A2` but not `A1`.**
- Some types are `A` but not `A2`.
- Some types are `A` but not `A1`.
- `A1` doesn't know about/depend on `A2`.
- `A2` doesn't know about/depend on `A1`.
- `A1` doesn't know about/depend on `A`.
- `A2` doesn't know about/depend on `A`.

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
# impl<T: AWrapper> A for T where T::AWrapped: AProxy<T = T> {
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

You may assume that `A`, `A1`, `A2` can be in separate crates with a dependency graph equivalent to this:

```rust
# mod __ {
mod a1 {
    pub trait A1 { /* ... */ }
    /* ... */
}

mod a2 {
    pub trait A2 { /* ... */ }
    /* ... */
}

mod a {
    use super::{a1, a2};
    pub trait A { /* ... */ }
    /* ... */
}

mod external {
    use super::{a::A, a1::A1, a2::A2};
    fn test(x: impl A) { /* ... */ }
    fn test1(x: impl A1) { /* ... */ }
    fn test2(x: impl A2) { /* ... */ }
}
# }
```

Template for a workspace:

```toml
# Cargo.toml
[workspace]
members = ["a", "a1", "a2", "common"]

[workspace.package]
version = "0.0.1"
edition = "2021"
publish = false

[package]
name = "solution"
version.workspace = true
edition.workspace = true
publish.workspace = true

[dependencies]
a.path = "a"
a1.path = "a1"
a2.path = "a2"
common.path = "common"
```

```toml
# a/Cargo.toml
[package]
name = "a"
version.workspace = true
edition.workspace = true
publish.workspace = true

[dependencies]
a1.path = "../a1"
a2.path = "../a2"
common.path = "../common"
```

```toml
# a1/Cargo.toml
[package]
name = "a1"
version.workspace = true
edition.workspace = true
publish.workspace = true

[dependencies]
common.path = "../common"
```

```toml
# a2/Cargo.toml
[package]
name = "a2"
version.workspace = true
edition.workspace = true
publish.workspace = true

[dependencies]
common.path = "../common"
```

```toml
# common.Cargo.toml
[package]
name = "common"
version.workspace = true
edition.workspace = true
publish.workspace = true
```

Basic definitions for `A1`/`A2` (this won't work&ensp;&mdash;&ensp;you need to alter it)

```rs
trait A1 {
    fn a1_ref(&self) -> u64;
    fn a1_mut(&mut self) -> bool;
    fn a1_move(self) -> Self;
}
```

```rs
trait A2 {
    fn a2_ref(&self) -> u64;
    fn a2_mut(&mut self) -> bool;
    fn a2_move(self) -> Self;
}
```
