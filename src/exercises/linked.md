# `Linked`

Fix (many) errors to make this compile and pass tests:

```rust
# #[derive(Clone, Copy)]
struct Node<'a> {
    next: Option<&'a Self>,
    value: &'a str,
}

struct Linked<'a, F> {
    node: Node<'a>,
    callback: &'a mut F,
}

impl<'a, F: FnMut(String)> Linked<'a, F> {
# fn __() {
# mod __ {
# struct Node<'a> { next: Option<Box<Self>>, value: &'a str }
# struct Linked<'a, F> { node: Box<Node<'a>>, callback: &'a mut F }
# impl<F> Linked<'_, F> {
    fn with(&mut self, value: &str) -> Linked<'_, F> {
# unimplemented!() } fn with2(self) { let _ =
        Self {
            node: Node {
                next: Some(self.node),
# value: unimplemented!(),
# }.into(),
# callback: self.callback,
# }; } } } }
# fn with<'b>(&'b mut self, value: &'b str) -> Linked<'b, F> {
# Linked {
# node: Node {
# next: Some(&self.node),
                value,
            },
            callback: self.callback,
        }
    }

    fn call(&mut self) {
        let mut node = self.node;
        let mut value = node.value.to_string();
        while let Some(next) = node.next {
            value += next.value;
# impl<'a, F: FnMut(String)> Linked<'a, F> { fn ___(&mut self) { trait Callback { fn callback(&mut self, value: String); }
# impl<'a, F: FnMut(String)> Callback for Linked<'a, F> { fn callback(&mut self, value: String) { (self.callback)(value); } }
# { let mut node = 1; let next = 2;
            node = next;
        }
# let value = unimplemented!();
        self.callback(value);
# } }
# node = *next; }
# (self.callback)(value);
    }
}

// don't change anything below

let mut vec = vec![];

let s = "0".to_string();

let mut linked = Linked {
    node: Node { next: None, value: &s },
    callback: &mut |value| vec.push(value),
};

// why does removing code below cause the instantiation to fail?

linked.call();
{
    let s = "1".to_string();
    let mut linked = linked.with(&s);
    linked.with("2").call();
    linked.with("3").call();
}
linked.with("4").call();

assert_eq!(vec, ["0", "210", "310", "40"]);
```
