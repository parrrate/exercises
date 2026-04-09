# Deserialisation modes
Merge implementations of `ConsumesStream` and `Deterministic` for tuple.

It's recommended to first solve [this exercise](./multiple_blanket.md).

```rust
trait Stream: Sized {
    /// Try to read `n` bytes. Consumes the stream on failure.
    fn read_n<A, E>(
        self,
        n: usize,
        ok: impl FnOnce(&[u8]) -> A,
        err: impl FnOnce(&[u8]) -> E,
    ) -> Result<(A, Self), E>;

    /// Read all bytes, consuming the stream.
    fn read_all<A>(self, ok: impl FnOnce(&[u8]) -> A) -> A;
}

/// Can be read from a stream.
trait FromStream: Sized {
    type Error;
}

/// Can be constructed from data of any length. Always consumes the stream.
trait ConsumesStream: FromStream {
    /// Try to read the value. Can handle EOF, can ask for all data the stream has.
    fn read_from(stream: impl Stream) -> Result<Self, Self::Error>;

    /// Push extra data into the value.
    fn extend(self, data: &[u8]) -> Result<Self, Self::Error>;
}

/// Whether the reading is terminated, is only determined by the data already read.
trait Deterministic: FromStream {
    /// Returns the stream, as it shouldn't succeed on unexpected EOF.
    fn read_from<S: Stream>(stream: S) -> Result<(Self, S), Self::Error>;

    /// Always fail on extra data.
    fn fail(data: &[u8]) -> Self::Error;
}

enum Either<L, R> {
    Left(L),
    Right(R),
}

impl<A: FromStream, B: FromStream> FromStream for (A, B) {
    type Error = Either<A::Error, B::Error>;
}

impl<A: Deterministic, B: ConsumesStream> ConsumesStream for (A, B) {
    fn read_from(stream: impl Stream) -> Result<Self, Self::Error> {
        let (a, stream) = A::read_from(stream)
            .map_err(Either::Left)?;
        B::read_from(stream)
            .map_err(Either::Right)
            .map(|b| (a, b))
    }

    fn extend(self, data: &[u8]) -> Result<Self, Self::Error> {
        self.1.extend(data)
            .map_err(Either::Right).map(|b| (self.0, b))
    }
}

impl<A: Deterministic, B: Deterministic> Deterministic for (A, B) {
    fn read_from<S: Stream>(stream: S) -> Result<(Self, S), Self::Error> {
        let (a, stream) = A::read_from(stream)
            .map_err(Either::Left)?;
        B::read_from(stream)
            .map_err(Either::Right)
            .map(|(b, stream)| ((a, b), stream))
    }

    fn fail(data: &[u8]) -> Self::Error {
        Either::Right(B::fail(data))
    }
}
```
