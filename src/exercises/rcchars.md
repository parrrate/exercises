# Owned [`Chars`]

[`Chars`] but keeping a strong reference ([`Rc`]) to the string.

[`Chars`]: https://doc.rust-lang.org/std/str/struct.Chars.html
[`Rc`]: https://doc.rust-lang.org/std/rc/struct.Rc.html

```rust
# mod rcchars {
# pub struct RcChars { _rc: std::rc::Rc<String>, chars: std::mem::MaybeUninit<std::str::Chars<'static>> }
# impl Iterator for RcChars {
# type Item = char;
# fn next(&mut self) -> Option<Self::Item> { unsafe { self.chars.assume_init_mut() }.next() }
# }
# impl RcChars {
# pub fn from_rc(rc: std::rc::Rc<String>) -> Self {
# let mut new = Self { _rc: rc, chars: std::mem::MaybeUninit::uninit() };
# new.chars .write(unsafe { &*std::rc::Rc::as_ptr(&new._rc) }.chars());
# new
# }
# }
# impl From<std::rc::Rc<String>> for RcChars {
# fn from(value: std::rc::Rc<String>) -> Self { Self::from_rc(value) }
# }
# impl Drop for RcChars {
# fn drop(&mut self) { unsafe { self.chars.assume_init_drop() } }
# }
# }
# use std::rc::Rc;
# use rcchars::RcChars;
{
    let rc = Rc::new("abc".to_string());
    let rcc = RcChars::from(rc.clone());
    drop(rc);
    assert_eq!(rcc.collect::<Vec<_>>(), ['a', 'b', 'c']);
}
{
    let rc = Rc::new("abc".to_string());
    let rcc = RcChars::from(rc);
    let rcc = Box::new(rcc);
    assert_eq!(rcc.collect::<Vec<_>>(), ['a', 'b', 'c']);
}
```

## Solutions
- [Implementation] used in rattlescript.
- [Same solution] but with some extra comments.
- [Another solution]. I prefer this one.
- There's an even simpler version that even an AI can generate (don't do that for `unsafe` code in production), but I dislike it for several reasons, even though it's mostly sound.

[Implementation]: https://github.com/HavenSelph/rattlescript/blob/f8bafb8b063b9bf056efb1ea14188db0624d981c/src/interpreter/value.rs#L21-L50
[Same solution]: https://gist.github.com/timotheyca/7e46c9734653b2fcbe826ea4d13b9aa0
[Another solution]: https://gist.github.com/timotheyca/bc419e0997d6f7ea5722fd2823a78c62
