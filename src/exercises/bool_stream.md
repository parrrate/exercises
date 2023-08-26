# Introducing variance to objects

Make `test_covariance` compile by making `BoolStream<'a>` covariant over `'a`. Restrictions:

- Can only change implementation details of `BoolStream` and its methods and add extra items outside of what's given, i.e. no signature/test change.
- Changed version must behave the same way as the original.

Consider the following code:

```rust,compile_fail
pub struct BoolStream<'a>(
    Box<dyn 'a + FnOnce() -> (bool, BoolStream<'a>)>,
);

impl<'a> BoolStream<'a> {
    pub fn new(f: impl 'a + FnOnce() -> (bool, Self)) -> Self {
        Self(Box::new(f))
    }

    pub fn next(self) -> (bool, Self) {
        self.0()
    }
}

fn test_covariance<'a: 'b, 'b>(e: BoolStream<'a>) -> BoolStream<'b> {
    e
}
```

Why does it fail to compile?

```rust
pub struct BoolStream<'a>(
    // change this
# /*
    Box<dyn 'a + FnOnce() -> (bool, BoolStream<'a>)>,
# */ Box<dyn 'a + Next>
);

impl<'a> BoolStream<'a> {
    pub fn new(f: impl 'a + FnOnce() -> (bool, Self)) -> Self {
        // maybe change this
# /*
        Self(Box::new(f))
# */ Self(Box::new(Wrapper(f, PhantomData)))
    }

    pub fn next(self) -> (bool, Self) {
        // maybe change this
# /*
        self.0()
# */ self.0.next()
    }
}

fn test_covariance<'a: 'b, 'b>(e: BoolStream<'a>) -> BoolStream<'b> {
    e
}

# use std::marker::PhantomData;
# struct Wrapper<'a, F>(F, PhantomData<&'a ()>);
# trait Next {
# fn next<'t>(self: Box<Self>) -> (bool, BoolStream<'t>) where Self: 't;
# }
# impl<'a, F: 'a + FnOnce() -> (bool, BoolStream<'a>)> Next for Wrapper<'a, F> {
# fn next<'t>(self: Box<Self>) -> (bool, BoolStream<'t>) where Self: 't { self.0() }
# }

/// Tests to check that behaviour stays the same.

fn get_true<'a>() -> BoolStream<'a> {
    BoolStream::new(|| (true, get_false()))
}

fn get_false<'a>() -> BoolStream<'a> {
    BoolStream::new(|| (false, get_true()))
}

let e = get_true();
let (x, e) = e.next();
assert!(x);
let (x, e) = e.next();
assert!(!x);
let (x, e) = e.next();
assert!(x);
let (x, e) = e.next();
assert!(!x);
test_covariance(e);
```
