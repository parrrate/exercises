# Report all errors (or only the first error)

Make this compile and pass tests:

```rust
# trait Collects {
# type Fast;
# type Slow;
# type Output;
# fn create(&self, next: i32) -> Result<Self::Slow, Self::Fast>;
# fn update(&self, slow: &mut Self::Slow, next: i32);
# fn regularize(&self, result: Result<Result<Vec<i32>, Self::Slow>, Self::Fast>) -> Result<Vec<i32>, Self::Output>;
# }
# struct First;
# struct All;
# impl Collects for First {
# type Fast = i32;
# type Slow = std::convert::Infallible;
# type Output = i32;
# fn create(&self, next: i32) -> Result<Self::Slow, Self::Fast> { Err(next) }
# fn update(&self, slow: &mut Self::Slow, _next: i32) { match *slow {} }
# fn regularize(&self, result: Result<Result<Vec<i32>, Self::Slow>, Self::Fast>) -> Result<Vec<i32>, Self::Output> { result.map(|r| r.unwrap_or_else(|slow| match slow {} )) }
# }
# impl Collects for All {
# type Fast = std::convert::Infallible;
# type Slow = Vec<i32>;
# type Output = Vec<i32>;
# fn create(&self, next: i32) -> Result<Self::Slow, Self::Fast> { let mut slow = vec![]; slow.push(next); Ok(slow) }
# fn update(&self, slow: &mut Self::Slow, next: i32) { slow.push(next) }
# fn regularize(&self, result: Result<Result<Vec<i32>, Self::Slow>, Self::Fast>) -> Result<Vec<i32>, Self::Output> { result.unwrap_or_else(|fast| match fast {}) }
# }
# fn first() -> impl Collects<Output = i32> { First }
# fn all() -> impl Collects<Output = Vec<i32>> { All }
# fn _only_postivie<C: Collects>(numbers: Vec<i32>, c: &C) -> Result<Result<Vec<i32>, C::Slow>, C::Fast> {
# let mut state = Ok(vec![]);
# for x in numbers { state = if x > 0 { state.map(|mut vec| {vec.push(x); vec}) } else { Err(match state {
# Ok(_) => c.create(x)?,
# Err(mut slow) => { c.update(&mut slow, x) ; slow }
# }) } }
# Ok(state)
# }
# fn only_positive<C: Collects>(numbers: Vec<i32>, c: C) -> Result<Vec<i32>, C::Output> { c.regularize(_only_postivie(numbers, &c)) }
assert_eq!(only_positive(vec![1, -1, 2, -4], first()), Err(-1));
assert_eq!(only_positive(vec![1, 2, 3], first()), Ok(vec![1, 2, 3]));
assert_eq!(only_positive(vec![1, -1, 2, -4], all()), Err(vec![-1, -4]));
assert_eq!(only_positive(vec![1, 2, 3], all()), Ok(vec![1, 2, 3]));
```

- `only_positive(..., first())` should short-circuit on first non-positive number.
- `only_positive(..., all())` must collect all non-positive numbers.
