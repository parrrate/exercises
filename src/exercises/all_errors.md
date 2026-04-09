# Report all errors (or only the first error)

Make this compile and pass tests:

```rust
# trait Collects {
# type Error;
# type Output;
# fn push(&self, trace: Vec<i32>, next: i32) -> Result<Vec<i32>, Self::Error>;
# fn regularize(&self, result: Result<Result<Vec<i32>, Vec<i32>>, Self::Error>) -> Result<Vec<i32>, Self::Output>;
# }
# struct First;
# struct All;
# impl Collects for First {
# type Error = i32;
# type Output = i32;
# fn push(&self, _trace: Vec<i32>, next: i32) -> Result<Vec<i32>, Self::Error> { Err(next) }
# fn regularize(&self, result: Result<Result<Vec<i32>, Vec<i32>>, Self::Error>) -> Result<Vec<i32>, Self::Output> { result.map(|r| r.unwrap()) }
# }
# impl Collects for All {
# type Error = std::convert::Infallible;
# type Output = Vec<i32>;
# fn push(&self, mut trace: Vec<i32>, next: i32) -> Result<Vec<i32>, Self::Error> { trace.push(next); Ok(trace) }
# fn regularize(&self, result: Result<Result<Vec<i32>, Vec<i32>>, Self::Error>) -> Result<Vec<i32>, Self::Output> { result.unwrap() }
# }
# fn first() -> impl Collects<Output = i32> { First }
# fn all() -> impl Collects<Output = Vec<i32>> { All }
# fn _only_postivie<C: Collects>(numbers: Vec<i32>, c: &C) -> Result<Result<Vec<i32>, Vec<i32>>, C::Error> {
# let mut state = Ok(vec![]);
# for x in numbers { state = if x > 0 { state.map(|mut vec| {vec.push(x); vec}) } else { Err(c.push(match state { Ok(_) => vec![], Err(vec) => vec }, x)?) } }
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
