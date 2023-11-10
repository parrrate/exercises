# `Err(Err)`

1. Implement the function for both groups of tests.
2. Provide usecases for each group.
3. How does this relate to the rest of this chapter (generic functional programming)?

```rust
// `try_join` or `zip` may be a better name
fn join<A, B, E0, E1>(
    r0: Result<Result<A, E0>, E1>,
    r1: Result<Result<B, E0>, E1>,
) -> Result<Result<(A, B), E0>, E1> {
# /*
    todo!()
# */
# Ok((|r0, r1| Ok((r0?, r1?)))(r0?, r1?))
}

# fn join_alt<A, B, E0, E1>(
# r0: Result<Result<A, E0>, E1>,
# r1: Result<Result<B, E0>, E1>,
# ) -> Result<Result<(A, B), E0>, E1> {
# match (r0, r1) {
# (Ok(Err(e)), _) | (_, Ok(Err(e))) => Ok(Err(e)),
# (Err(e), _) | (_, Err(e)) => Err(e),
# (Ok(Ok(a)), Ok(Ok(b))) => Ok(Ok((a, b))),
# }
# }
# let mut short = false;
# for join in [join, join_alt] {
# let tested = |
# r0: Result<Result<i32, i32>, &'static str>,
# r1: Result<Result<&'static str, i32>, &'static str>
# | join(r0, r1);
# let join = tested;
// applies to both
assert_eq!(join(Ok(Ok(0)), Ok(Ok("1"))), Ok(Ok((0, "1"))));
assert_eq!(join(Ok(Err(2)), Ok(Ok("3"))), Ok(Err(2)));
assert_eq!(join(Ok(Ok(3)), Ok(Err(4))), Ok(Err(4)));
assert_eq!(join(Ok(Err(5)), Ok(Err(5))), Ok(Err(5)));
assert_eq!(join(Err("6"), Ok(Ok("7"))), Err("6"));
assert_eq!(join(Ok(Ok(8)), Err("9")), Err("9"));
assert_eq!(join(Err("10"), Err("10")), Err("10"));
// the following two groups are mutually exclusive
# if short {
    // group 1
assert_eq!(join(Ok(Err(11)), Err("12")), Ok(Err(11)));
assert_eq!(join(Err("13"), Ok(Err(14))), Ok(Err(14)));
# } else {
    // group 2
assert_eq!(join(Ok(Err(11)), Err("12")), Err("12"));
assert_eq!(join(Err("13"), Ok(Err(14))), Err("13"));
# short = true;
# }
# }
```
