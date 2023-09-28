# Async `to_string`

Define the `AsyncToString` trait to make this compile and pass tests (don't edit existing code):

```rust,ignore,mdbook-runnable
# extern crate futures;
# use futures::{Future, executor::block_on};
# trait AsyncToStringRef<'a> { type F: 'a + Future<Output = String>; fn to_string(&'a self, s: &'a str) -> Self::F; }
# impl<'a, F: 'a + Future<Output = String>, T: Fn(&'a str) -> F> AsyncToStringRef<'a> for T {
# type F = F;
# fn to_string(&'a self, s: &'a str) -> Self::F { self(s) }
# }
# trait AsyncToString: for<'a> AsyncToStringRef<'a> {}
# impl<T: for<'a> AsyncToStringRef<'a>> AsyncToString for T {}
async fn test_generic(s: &str, f: impl AsyncToString) -> String {
    f.to_string(s).await
}

async fn to_string_async(s: &str) -> String {
    s.to_string()
}

async fn test_concrete(s: &str) -> String {
    test_generic(s, to_string_async).await
}

assert_eq!(block_on(test_concrete("abc")), "abc".to_string())
```
