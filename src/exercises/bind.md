Make this compile and pass tests:
```rust
# mod __ {
fn bind<T, F: Fn(T) -> Option<T>>(f: F, fa: Option<T>) -> Option<T> {
    match fa {
        Some(a) => f(a),
        None => None,
    }
}
# }
# fn bind<T, F: FnOnce(T) -> Option<T>>(f: F, fa: Option<T>) -> Option<T> { fa.and_then(f) }

// don't change anything below

assert_eq!(bind(|x: i32| Some(x + 3), Some(2)), Some(5));
assert_eq!(bind(|x: i32| Some(x + 3), None), None);
assert_eq!(bind(|_: i32| None, Some(2)), None);
assert_eq!(bind(|_: i32| None, None), None);

assert_eq!(bind(|x: &str| Some(x), Some("apple")), Some("apple"));
assert_eq!(bind(|_: &str| Some("banana"), Some("apple")), Some("banana"));

let banana = "banana".to_string();
assert_eq!(
    bind(|_: String| Some(banana), Some("apple".to_string())),
    Some("banana".to_string()),
);
```

Try solving it in the playground:
```rust,editable,compile_fail
fn bind<T, F: Fn(T) -> Option<T>>(f: F, fa: Option<T>) -> Option<T> {
    match fa {
        Some(a) => f(a),
        None => None,
    }
}

fn main() {
    assert_eq!(bind(|x: i32| Some(x + 3), Some(2)), Some(5));
    assert_eq!(bind(|x: i32| Some(x + 3), None), None);
    assert_eq!(bind(|_: i32| None, Some(2)), None);
    assert_eq!(bind(|_: i32| None, None), None);

    assert_eq!(bind(|x: &str| Some(x), Some("apple")), Some("apple"));
    assert_eq!(bind(|_: &str| Some("banana"), Some("apple")), Some("banana"));

    let banana = "banana".to_string();
    assert_eq!(
        bind(|_: String| Some(banana), Some("apple".to_string())),
        Some("banana".to_string()),
    );
}
```
