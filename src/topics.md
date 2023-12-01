# Topics

Rows are ordered based on estimated difficulty.

| lifetimes\[-adjacent\] | `trait`s           | `struct`s           |
|------------------------|--------------------|---------------------|
| [explicit lifetimes]   | [`Fn`?]            | [`Duration`]        |
| [from reference]       | [`curry`]          | [All Errors?]       |
| [extracting lifetimes] | [`async` `Fn`]     | [`RcChars`]         |
| [`'static` return]     | [exclusive traits] | [`Result` ordering] |
| [composition]          | [merging traits]   | [`AnyStr`]          |

p.s. most of the exercises are actually about `trait`s, this categorisation isn't strict.

[explicit lifetimes]: ./exercises/refbind.md
[`Fn`?]: ./exercises/bind.md
[`RcChars`]: ./exercises/rcchars.md
[extracting lifetimes]: ./exercises/bool_stream.md
[exclusive traits]: ./exercises/multiple_blanket.md
[merging traits]: ./exercises/mode.md
[`AnyStr`]: ./exercises/anystr.md
[composition]: ./exercises/composition.md
[`Duration`]: ./exercises/duration.md
[All Errors?]: ./exercises/all_errors.md
[from reference]: ./exercises/fromref.md
[`async` `Fn`]: ./exercises/async_fn.md
[`'static` return]: ./exercises/get_functions.md
[`Result` ordering]: ./exercises/err_err.md
[`curry`]: ./exercises/curry.md
