# cargo-expand

[![Build Status](https://travis-ci.org/dtolnay/cargo-expand.svg?branch=master)](https://travis-ci.org/dtolnay/cargo-expand)
[![Latest Version](https://img.shields.io/crates/v/cargo-expand.svg)](https://crates.io/crates/cargo-expand)

This is a wrapper around `cargo rustc -- --pretty=expanded`. Once installed, the
command `cargo expand` prints out the result of macro expansion and `#[derive]`
expansion applied to the current crate.

## Installation

Install with `cargo install cargo-expand`.

This command optionally uses
[`rustfmt`](https://github.com/rust-lang-nursery/rustfmt)
to format the expanded output. The resulting code is typically much more
readable than what you get from the compiler. If `rustfmt` is not available, the
expanded code is not formatted. Install `rustfmt` with `cargo install rustfmt`.

This command optionally uses [`Pygments`](http://pygments.org/) to colorize the
expanded output. If `Pygments` is not available, the expanded code is not
colorized. Install with `pip install Pygments`.

## Example

`$ cat src/main.rs`

> ```rust
> #[derive(Debug)]
> struct S;
>
> fn main() {
>     println!("{:?}", S);
> }
> ```

`$ cargo expand`

> ```rust
> #![feature(prelude_import)]
> #![no_std]
> #[prelude_import]
> use std::prelude::v1::*;
> #[macro_use]
> extern crate std as std;
> struct S;
> #[automatically_derived]
> #[allow(unused_qualifications)]
> impl ::std::fmt::Debug for S {
>     fn fmt(&self, __arg_0: &mut ::std::fmt::Formatter) -> ::std::fmt::Result {
>         match *self {
>             S => {
>                 let mut builder = __arg_0.debug_tuple("S");
>                 builder.finish()
>             }
>         }
>     }
> }
>
> fn main() {
>     ::io::_print(::std::fmt::Arguments::new_v1({
>                                                    static __STATIC_FMTSTR:
>                                                           &'static [&'static str]
>                                                           =
>                                                        &["", "\n"];
>                                                    __STATIC_FMTSTR
>                                                },
>                                                &match (&S,) {
>                                                    (__arg0,) =>
>                                                     [::std::fmt::ArgumentV1::new(__arg0,
>                                                                                  ::std::fmt::Debug::fmt)],
>                                                }));
> }
> ```

## Options

To expand a particular test target:

`$ cargo expand --test test_something`

To expand with `rustfmt` different from the one in `$PATH`:

`$ RUSTFMT=/path/to/rustfmt cargo expand`

To expand without `rustfmt` even though it is available in `$PATH`:

`$ RUSTFMT= cargo expand`

To color with `pygmentize` different from the one in `$PATH`:

`$ PYGMENTIZE=/path/to/pygmentize cargo expand`

To not color even though `pygmentize` is available in `$PATH`:

`$ PYGMENTIZE= cargo expand`

## Disclaimer

Be aware that macro expansion to text is a lossy process. This is a debugging
aid only. There should be no expectation that the expanded code can be compiled
successfully, nor that if it compiles then it behaves the same as the original
code.

For instance the following function returns `3` when compiled ordinarily by Rust
but the expanded code compiles and returns `4`.

```rust
fn f() -> i32 {
    let x = 1;

    macro_rules! first_x {
        () => { x }
    }

    let x = 2;

    x + first_x!()
}
```

Refer to [The Book] for more on the considerations around macro hygiene.

[The Book]: https://doc.rust-lang.org/book/first-edition/macros.html#hygiene

## License

Licensed under either of

 * Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE) or http://www.apache.org/licenses/LICENSE-2.0)
 * MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

at your option.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in cargo-expand by you, as defined in the Apache-2.0 license,
shall be dual licensed as above, without any additional terms or conditions.
