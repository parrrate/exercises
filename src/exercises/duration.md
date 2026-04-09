# `const` `Add`?

Define `FIVE_SECONDS` as `const`:

```rust
# use std::time::Duration;
# const fn unwrap<T: Copy>(o: Option<T>) -> T { match o { Some(t) => t, None => panic!() } }
const TWO_SECONDS: Duration = Duration::from_secs(2);
# /*
const FIVE_SECONDS: Duration = TWO_SECONDS + Duration::from_secs(3);
# */
# const FIVE_SECONDS: Duration = unwrap(TWO_SECONDS.checked_add(Duration::from_secs(3)));
assert_eq!(FIVE_SECONDS, Duration::from_secs(5));
```

Try solving it in the playground:

```rust,editable,compile_fail
use std::time::Duration;

const TWO_SECONDS: Duration = Duration::from_secs(2);
const FIVE_SECONDS: Duration = TWO_SECONDS + Duration::from_secs(3);

fn main() {
    assert_eq!(FIVE_SECONDS, Duration::from_secs(5));
}
```
