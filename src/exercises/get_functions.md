# Get `Functions`

Edit the body of `get_functions` to make this compile and pass tests:

```rust
struct Functions {
    five: fn() -> i32,
    increment: fn(i32) -> i32,
}

fn get_functions() -> &'static Functions {
# /*
    let five = || 5;
    let increment = |n| n + 1;
    let functions = Functions { five, increment };
    &functions
# */
# &Functions { five: || 5, increment: |n| n + 1 }
}

assert_eq!((get_functions().five)(), 5);
assert_eq!((get_functions().increment)(4), 5);
```

Try solving it in the playground:

```rust,editable,compile_fail
struct Functions {
    five: fn() -> i32,
    increment: fn(i32) -> i32,
}

fn get_functions() -> &'static Functions {
    let five = || 5;
    let increment = |n| n + 1;
    let functions = Functions { five, increment };
    &functions
}

fn main() {
    assert_eq!((get_functions().five)(), 5);
    assert_eq!((get_functions().increment)(4), 5);
}
```
