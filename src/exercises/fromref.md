Make this compile and pass tests:

```rust
fn with_slice<T>(f: impl FnOnce(&str) -> T) -> T {
    let s = "te".to_string() + "st";
    f(&s)
}

# pub trait FromRef<T: ?Sized>: for<'a> From<&'a T> { fn from_ref(value: &T) -> Self { Self::from(value) } }
# impl<T: ?Sized, U: for<'a> From<&'a T>> FromRef<T> for U {}
let string = with_slice(
# /*
    String::from
# */ String::from_ref
);
assert_eq!(string, "test".to_string());
```

Try solving it in the playground:

```rust,editable,compile_fail
fn with_slice<T>(f: impl FnOnce(&str) -> T) -> T {
    f("test")
}

fn main() {
    let string = with_slice(
        String::from // Change this
    );
    assert_eq!(string, "test".to_string());
}
```
