# Currying

Implement `curry` and make it pass the tests.

```rust
# trait Curried<A, B>: FnOnce(A) -> Self::Inner {
# type C;
# type Inner: FnOnce(B) -> Self::C;
# }
# impl<A, B, C, Inner: FnOnce(B) -> C, F: FnOnce(A) -> Inner> Curried<A, B> for F {
# type C = C;
# type Inner = Inner;
# }
# fn curry<A, B, C>(f: impl FnOnce(A, B) -> C) -> impl Curried<A, B, C = C> { |a| |b| f(a, b) }
assert_eq!(curry(|a, b| a * b)(3)(5), 15);
let mut x = 0;
curry(|x: &mut i32, y| *x = y)(&mut x)(5);
assert_eq!(x, 5);
```

Try solving it in the playground:

```rust,editable,compile_fail
fn main() {
    assert_eq!(curry(|a, b| a * b)(3)(5), 15);
    let mut x = 0;
    curry(|x: &mut i32, y| *x = y)(&mut x)(5);
    assert_eq!(x, 5);
}
```
