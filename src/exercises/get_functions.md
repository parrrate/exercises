# Get `Functions`

Edit the body of `get_functions` to make this compile and pass tests:

```rust
struct Functions {
    five: fn() -> i32,
    increment: fn(i32) -> i32,
}

fn get_functions<const STEP: i32>() -> &'static Functions {
# /*
    let five = || 5;
    let increment = |n| n + STEP;
    let functions = Functions { five, increment };
    &functions
# */
# &Functions { five: || 5, increment: |n| n + STEP }
}

assert_eq!((get_functions::<1>().five)(), 5);
assert_eq!((get_functions::<3>().five)(), 5);
assert_eq!((get_functions::<1>().increment)(4), 5);
assert_eq!((get_functions::<3>().increment)(4), 7);
```

Try solving it in the playground:

```rust,editable,compile_fail
struct Functions {
    five: fn() -> i32,
    increment: fn(i32) -> i32,
}

fn get_functions<const STEP: i32>() -> &'static Functions {
    let five = || 5;
    let increment = |n| n + STEP;
    let functions = Functions { five, increment };
    &functions
}

fn main() {
    assert_eq!((get_functions::<1>().five)(), 5);
    assert_eq!((get_functions::<3>().five)(), 5);
    assert_eq!((get_functions::<1>().increment)(4), 5);
    assert_eq!((get_functions::<3>().increment)(4), 7);
}
```
