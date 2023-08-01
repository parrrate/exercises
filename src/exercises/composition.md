```rust
# use std::ops::ControlFlow;

trait Monad<'a>: 'a {
    type W<A: 'a>: 'a;

    fn map<A: 'a, B: 'a>(
        wa: Self::W<A>,
        f: impl FnOnce(A) -> B,
    ) -> Self::W<B>;

    fn pure<A: 'a>(
        a: A,
    ) -> Self::W<A>;

    fn collect<A: 'a, B: 'a>(
        wab: (Self::W<A>, Self::W<B>),
    ) -> Self::W<(A, B)>;

    fn bind<A: 'a, B: 'a>(
        wa: Self::W<A>,
        f: impl FnOnce(A) -> Self::W<B>,
    ) -> Self::W<B>;

    fn iterate<A: 'a>(
        i: impl Iteration<'a, A = A, M = Self>,
    ) -> Self::W<A>;
}

trait Iteration<'a>: 'a + Sized {
    type A: 'a;

    type M: ?Sized + Monad<'a>;

    fn next(self) -> IWrap<'a, Self>;
}

type Wrap<'a, M, A> = <M as Monad<'a>>::W<A>;

type IOutput<'a, I> = ControlFlow<<I as Iteration<'a>>::A, I>;

type IWrap<'a, I> = Wrap<'a, <I as Iteration<'a>>::M, IOutput<'a, I>>;

struct Composition<U, V>(U, V);

impl<'a, U, V> Monad<'a> for Composition<U, V>
where
    U: Monad<'a>,
    V: Monad<'a>,
    // something missing here?
# V: Local<'a>,
{
    type W<A: 'a> = U::W<V::W<A>>;

    fn map<A: 'a, B: 'a>(
        wa: Self::W<A>,
        f: impl FnOnce(A) -> B,
    ) -> Self::W<B> {
        U::map(wa, |va| V::map(va, f))
    }

    fn pure<A: 'a>(
        a: A,
    ) -> Self::W<A> {
        U::pure(V::pure(a))
    }

    fn collect<A: 'a, B: 'a>(
        wab: (Self::W<A>, Self::W<B>),
    ) -> Self::W<(A, B)> {
        U::map(U::collect(wab), V::collect)
    }

    fn bind<A: 'a, B: 'a>(
        wa: Self::W<A>,
        f: impl FnOnce(A) -> Self::W<B>,
    ) -> Self::W<B> {
        // impossible
# U::bind(wa, |va| U::map(V::wrap::<_, U>(V::map(va, f)), |vvb| V::bind(vvb, |vb| vb)))
    }

    fn iterate<A: 'a>(
        i: impl Iteration<'a, A = A, M = Self>,
    ) -> Self::W<A> {
        // impossible
# U::iterate(ComposedIteration(i))
    }
}

# trait Local<'a>: Monad<'a> { fn wrap<A: 'a, M: Monad<'a>>(wa: Self::W<M::W<A>>) -> M::W<Self::W<A>>; }
# struct CfInstance<E>(ControlFlow<(), E>);
# use ControlFlow::{Continue, Break};
# impl<'a, E: 'a> Monad<'a> for CfInstance<E> {
# type W<A: 'a> = ControlFlow<A, E>;
# fn map<A: 'a, B: 'a>(wa: Self::W<A>, f: impl FnOnce(A) -> B) -> Self::W<B> {
# match wa { Continue(c) => Continue(c), Break(a) => Break(f(a)) }
# }
# fn pure<A: 'a>(a: A) -> Self::W<A> { Break(a) }
# fn collect<A: 'a, B: 'a>(wab: (Self::W<A>, Self::W<B>)) -> Self::W<(A, B)> {
# match wab { (Continue(e), _) => Continue(e), (_, Continue(e)) => Continue(e), (Break(a), Break(b)) => Break((a, b)) }
# }
# fn bind<A: 'a, B: 'a>(wa: Self::W<A>, f: impl FnOnce(A) -> Self::W<B>) -> Self::W<B> {
# match wa { Continue(e) => Continue(e), Break(a) => f(a) }
# }
# fn iterate<A: 'a>(mut i: impl Iteration<'a, A = A, M = Self>) -> Self::W<A> {
# loop { match i.next() { Continue(e) => break Continue(e), Break(Continue(next_i)) => i = next_i, Break(Break(a)) => break Break(a) } }
# }
# }
# struct ComposedIteration<F>(F);
# impl<'a, U: Monad<'a>, V: Local<'a>, F: Iteration<'a, M = Composition<U, V>>> Iteration<'a> for ComposedIteration<F> {
# type A = Wrap<'a, V, F::A>;
# type M = U;
# fn next(self) -> IWrap<'a, Self> { U::map(self.0.next(), |vstate| { ControlFlow::Continue(Self(V::wrap::<_, CfInstance<_>>(vstate)?)) }) }
# }
```
