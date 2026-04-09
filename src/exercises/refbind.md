Make this compile and pass tests.
```rust
# mod __ {
fn refbind<T, F: Fn(&T) -> Option<&T>>(f: F, fa: Option<&T>) -> Option<&T> {
    match fa {
        Some(a) => f(a),
        None => None,
    }
}
# }
# fn refbind<'a,'b,T:'a+?Sized>(f:impl 'a+Fn(&'a T)->Option<&'b T>,fa:Option<&'a T>)->Option<&'b T>{fa.and_then(f)}

// don't change anything below

let apple = "apple".to_string();
let banana = "banana".to_string();
assert_eq!(
    refbind(|_: &String| Some(&banana), Some(&apple)),
    Some(&banana)
);

let banana = "banana";
assert_eq!(
    refbind(|_: &str| Some(banana), Some("apple")),
    Some(banana)
);
```

Try solving it in the playground:
```rust,editable,compile_fail
fn refbind<T, F: Fn(&T) -> Option<&T>>(f: F, fa: Option<&T>) -> Option<&T> {
    match fa {
        Some(a) => f(a),
        None => None,
    }
}

fn main() {
    let apple = "apple".to_string();
    let banana = "banana".to_string();
    assert_eq!(
        refbind(|_: &String| Some(&banana), Some(&apple)),
        Some(&banana)
    );

    let banana = "banana";
    assert_eq!(
        refbind(|_: &str| Some(banana), Some("apple")),
        Some(banana)
    );
}
```

