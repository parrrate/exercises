# Calculating with Functions (based on Codewars)

Original: [Calculating with Functions](https://www.codewars.com/kata/525f3eda17c7cd9f9e000b39)

As you might know, on *stable* Rust you can't have variable number of arguments passed to a
function. However we can alter the exercise to work anyway.

```rust
# trait Value { fn value(self) -> i32; }
# trait Op { fn apply(self, value: i32) -> i32; }
# struct Identity;
# impl Op for Identity { fn apply(self, value: i32) -> i32 { value } }
# impl<F: FnOnce(i32) -> i32> Op for F { fn apply(self, value: i32) -> i32 { self(value) } }
# impl<F: FnOnce(Identity) -> i32> Value for F { fn value(self) -> i32 { self(Identity) } }
# impl Value for i32 { fn value(self) -> i32 { self } }
# fn zero(f: impl Op) -> i32 { f.apply(0) }
# fn one(f: impl Op) -> i32 { f.apply(1) }
# fn two(f: impl Op) -> i32 { f.apply(2) }
# fn three(f: impl Op) -> i32 { f.apply(3) }
# fn four(f: impl Op) -> i32 { f.apply(4) }
# fn five(f: impl Op) -> i32 { f.apply(5) }
# fn six(f: impl Op) -> i32 { f.apply(6) }
# fn seven(f: impl Op) -> i32 { f.apply(7) }
# fn eight(f: impl Op) -> i32 { f.apply(8) }
# fn nine(f: impl Op) -> i32 { f.apply(9) }
# fn plus(x: impl Value) -> impl FnOnce(i32) -> i32 { |y| y + x.value() }
# fn minus(x: impl Value) -> impl FnOnce(i32) -> i32 { |y| y - x.value() }
# fn times(x: impl Value) -> impl FnOnce(i32) -> i32 { |y| y * x.value() }
# fn divided_by(x: impl Value) -> impl FnOnce(i32) -> i32 { |y| y / x.value() }
assert_eq!(seven(times(five)), 35);
assert_eq!(four(plus(nine)), 13);
assert_eq!(eight(minus(three)), 5);
assert_eq!(six(divided_by(two)), 3);
```
