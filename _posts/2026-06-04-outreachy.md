---
title: "Bridging Rust and C++: An Outreachy Intern's Three-Month Journey"
description: >-
  My Outreachy internship report: three months working on Rust-to-C++ interoperability,
  what brought me here, and what I hope to build.
author: ajay
date: 2026-06-04
categories: [Open Source, Outreachy]
tags: [Outreachy, Rust, C++, FFI, Systems Programming]
pin: true
---

## Hello, World!

I got accepted to Outreachy in the May - Aug (2026) cohort , I'll be working with Rust Foundation under the guidance of my mentors teor , Joel Marcey , Taylor Cramer and Ethan Smith. 

This post is my first internship report. I want to use it to introduce myself, tell you what brought me to Outreachy, and explain the project I will be spending the next three months on.

---

## What is Outreachy?

[Outreachy](https://www.outreachy.org/) provides remote, paid internships in open source and open science. It specifically supports people who face under-representation or systemic bias in the technology industry , including women, people of colour, people with disabilities, and others who are not well represented in tech.

What makes Outreachy different from typical internship programmes is its intentional focus on equity. The application process is structured to give everyone a fair shot, regardless of institutional pedigree or prior visibility in a community. What matters is what you do during the contribution period.

---

## My Community and Project

I am working with the **Rust Foundation** on their [Interoperability Initiative](https://github.com/rustfoundation/interop-initiative), a collaborative effort between the Rust Foundation, the Rust Project, and external stakeholders to make C++ and Rust interoperability accessible to the widest possible audience.

My specific project for the three-month internship is:

> **Calling C++ Overloaded Functions from Rust**

Rust and C++ are both systems programming languages, but they have very different approaches to the same problems. C++ has had *function overloading* for decades , the ability to define multiple functions with the same name but different parameter types. Rust, by design, does not have overloading in the same sense.

When Rust code needs to call into a C++ library (via the Foreign Function Interface, or FFI), overloaded functions present a specific challenge. The C++ compiler *mangles* function names , it encodes the parameter types into the symbol name to distinguish between overloads. Rust's FFI layer speaks C, not C++, so it cannot directly call C++ overloaded functions without extra plumbing.

My project is about solving exactly this problem: enabling Rust code to call C++ overloaded functions cleanly, correctly, and in a way that fits naturally into the Rust ecosystem.

This work matters because a huge amount of the world's systems software is written in C++. Making it easier to interoperate with Rust , without rewriting everything , has real practical value for the open source ecosystem.

---

## What Motivated Me to Apply

I had completed the [LFX Mentorship](https://lfx.linuxfoundation.org/tools/mentorship/) programme earlier, where I worked on modernising the Istio CI/CD pipeline. That experience showed me what sustained, structured contribution to a real codebase feels like. It also made me want more.

But Outreachy offered something LFX did not: an explicit commitment to equity. I come from a background where access to high-quality mentorship, networking, and educational resources has never been guaranteed. When I read Outreachy's values, I recognised something that felt directly relevant to my experience.

I applied because I believed I had something real to contribute , and because Outreachy's structure gave me a genuine chance to demonstrate that.

What kept me going during the application stages was the contribution period itself. Rather than asking candidates to write essays or pass theoretical tests, Outreachy asks you to make actual contributions to the project. That suited me perfectly. Working on a real codebase, submitting patches, getting review feedback , that is how I learn best.

---

## The Contribution Period

During the contribution period, I worked directly with the Rust Foundation's interop-initiative repository and the `rust-clippy` linter. I got hands-on with C++/Rust string interoperability, Rust's linting infrastructure, and what it actually takes to contribute to a large, carefully maintained open source project.

Some things I explored and learned:

- How the Rust/C++ string boundary creates performance, safety, and adoption friction for real-world projects
- How Rust's `clippy` linter is structured internally, and how edge cases in the Rust lexer can create subtle false negatives
- What good open source review culture looks like , iterating on feedback, answering questions in plain language, and building consensus before changing code

---

## My Proudest Contributions during contribution period of Outreachy

### 1. Fix false negative in `needless_ifs` — [rust-clippy #16845](https://github.com/rust-lang/rust-clippy/pull/16845)

Rust's lexer treats vertical tab (`\x0B`) as whitespace, but the `is_ascii_whitespace()` method does not. This mismatch caused the `needless_ifs` lint to silently miss cases where vertical tab appeared in the source , a false negative that could let redundant code slip through undetected.

I tracked down the root cause, fixed the whitespace check, and navigated the challenge of writing a test for it: `rustfmt` strips vertical tabs during formatting, so preserving the test character required careful use of `#![rustfmt::skip]` and a separate test file to avoid touching the main test suite. Getting that detail right required multiple rounds of review and iteration , which turned out to be one of the most valuable parts of the process.

### 2. Fix examples directory path in documentation — [interop-initiative #37](https://github.com/rustfoundation/interop-initiative/pull/37)

The contributing documentation referenced a path `interop/examples`, but the actual repository is named `interop-initiative`. Anyone following the setup instructions would hit a dead end immediately. A small fix, but one that makes the project more accessible to new contributors , which is exactly what the Interoperability Initiative is trying to do.

### 3. Add Impact and Unresolved Questions to the string interop problem statement — [interop-initiative #17](https://github.com/rustfoundation/interop-initiative/pull/17)

The string interoperability problem statement had two sections left as `TODO`: an Impact section and an Unresolved Questions section. I filled both in, documenting the key pain points of Rust/C++ string interop , performance overhead from copying and re-encoding, safety risks at the boundary, adoption barriers for mixed codebases, and the way string type mismatches shrink the usable API surface on both sides.

---

## What I Expect from This Internship

I want to come out of these three months with three things.

First, **deep technical understanding** of Rust's FFI model and C++ ABI conventions. This is the kind of systems-level knowledge that takes years to accumulate informally. Having a structured project to anchor the learning makes it possible to go much deeper, much faster.

Second, **experience shipping real open source work**  not just patches, but design decisions, documentation, and the kind of back-and-forth that turns an idea into something maintainable by others.

Third, **relationships with engineers who care about the craft**. Getting guidance from senior engineers working on the Rust compiler is something I value deeply. The direction I receive through this internship from engineers operating at that level is something I will carry with me for the rest of my career.

I am under no illusions that this will be easy. C++ and Rust represent two different philosophies of systems programming, and the places where they meet are genuinely complex. But that complexity is exactly what makes this work interesting.

---

## Weeks 1-2: Building a Foundation
 
I spent the first two weeks of the internship not writing any code that would end up in a pull request. Instead I set up a full local build of the Rust compiler, which on its own is a nontrivial exercise, the `rustc` bootstrap process compiles a stage 0 compiler from a downloaded snapshot, uses it to build stage 1, and can go further to stage 2 depending on what you're testing. Getting `./x test` to run reliably, understanding the difference between stages, and learning to read compiler diagnostics well enough to debug my own toolchain issues took real time.
 
Alongside that I worked through *the Rust book* to shore up gaps in my understanding, particularly around traits, generics, and the trait object system, all of which turned out to matter directly once I started working with `#[splat]`.
 
This groundwork mattered more than it might sound. Nearly every problem I hit in the following weeks, a broken submodule fetch, a stale build cache reporting a test as "up to date" when it wasn't, a `--force-with-lease` push that needed to be understood rather than just copy-pasted, came back to fundamentals I'd built in these first two weeks.
 
## Weeks 3-6: Testing `#[splat]`
 
My mentor teor was the author of an experimental compiler feature called `#[splat]`, part of the argument-splatting lang experiment underpinning the C++ overloading project goal. `#[splat]` lets a function argument that is a tuple be "splatted" into individual arguments at the call site, the foundational mechanism that would eventually let Rust represent overloaded C++ functions using a single generic function with different tuple types.
 
My job was to stress-test it.
 
### Finding and fixing an ICE
 
While writing the "overloading at home" example tests, `splat-overload-at-home-fail.rs`, which check that calling a splatted generic function with an argument type that has no matching trait impl produces a normal type error rather than a crash, I hit an actual internal compiler error (ICE) instead of the clean diagnostic the test expected. Rather than just writing the test to expect a crash, I went into the compiler source to find out why.
 
The bug turned out to be a single inverted condition: a boolean check in the relevant compiler code path was negated when it shouldn't have been, so the compiler was taking the "this should never happen, ICE now" branch on a case that was actually valid and should have produced a regular diagnostic. Removing the stray `!` fixed the logic, and the test that had been crashing the compiler started passing as a normal, well-behaved type-error test. It was a small change, one character, but tracking it down meant reading unfamiliar parts of `rustc` under time pressure from a crash backtrace, and it was the first time in the internship I'd gone from "the compiler crashed" to "I understand why, and I fixed it" rather than just reporting it.
 
### Building out coverage
 
Over these four weeks I wrote UI tests covering:
 
- `const fn` with `#[splat]`
- `async fn` with `#[splat]`, including failing cases
- `unsafe fn` with `#[splat]`, including failing cases
- `where` clause bounds with `#[splat]`
- Complex generic types inside splatted tuples, such as `Vec<T>`, `Option<U>`, and `Box<T>`
- `const fn` combined with generics
- Generic traits with `#[splat]`
- `#[splat]` on `&dyn AsRef<T>` where `T: Tuple`
Several of these came directly from FIXME comments teor had left in his own test suite as reminders of coverage gaps. Removing a FIXME because you've written the test that resolves it is a small, satisfying kind of contribution.
 
The `dyn AsRef<T>` test was the most interesting one. Teor asked for a test proving that `#[splat]` should *not* work on `&dyn AsRef<T> where T: Tuple`, since `Tuple` itself isn't dyn-compatible and this combination shouldn't type-check. When I wrote it, the function definition compiled fine, and only failed once I supplied an actual call site with a concrete argument. That distinction, whether an error surfaces at definition time or only once you try to call the function, turned out to matter a lot for how the test needed to be written, and teor's review comments walked me through exactly why: with `#[splat]`, some invariants can only be checked once the compiler knows the concrete tuple type, which only happens at a call site.
 
Teor also gave me a broader piece of review feedback that shaped how I approached the rest of the internship: keep passing and failing tests in separate files rather than mixing `//@ run-pass` cases and `//~ ERROR` cases in the same test. It's a small convention, but it makes each test file's purpose unambiguous at a glance, and reviewers don't have to hold two different mental models while reading one file.
 
By the end of week 6, teor had cherry-picked my test commits into [rust-lang/rust#153697](https://github.com/rust-lang/rust/pull/153697), and my own PR, [rust-lang/rust#157434](https://github.com/rust-lang/rust/pull/157434), tracked his branch directly so the tests could keep evolving alongside the feature. #153697 has since merged into the compiler.
 
## Building the `overload!` Macro
 
With `#[splat]` merged, the next problem became obvious: `#[splat]` on its own is not ergonomic. To get C++-style overloading today, a developer has to hand-write a trait, one trait implementation per overload, and only then the actual splatted function. My mentor suggested I try turning that boilerplate into a macro.
 
We debated declarative macros (`macro_rules!`) versus procedural macros first. A declarative macro can pattern-match syntax, but it has no real way to parse a full function signature, extract argument names and types, and generate a new trait and impl blocks from them. A procedural macro, which is just a Rust function that receives your code as a token stream and returns generated code as a token stream, gives you the full power of `syn` for parsing and `quote` for code generation. We started there, with a concrete, deliberately small first target: two free functions, not methods, each with a single argument.
 
### What the macro needed to produce
 
Before writing any macro code, I manually wrote out, by hand, exactly what I wanted the macro to eventually generate, and confirmed it compiled and ran correctly on the Rust Playground with nightly and `#![feature(splat)]` enabled:
 
```rust
trait FooArgs: std::marker::Tuple {
    fn call(self);
}
 
impl FooArgs for (i32,) {
    fn call(self) {}
}
 
impl FooArgs for (f64,) {
    fn call(self) {}
}
 
fn foo<T: FooArgs>(#[splat] args: T) {
    args.call()
}
```
 
Having the target written out and verified first made writing the macro itself much more tractable, I always knew exactly what tokens I was trying to produce.
 
### The macro, piece by piece
 
The macro takes input like this:
 
```rust
overload! {
    fn foo(x: i32) { println!("i32: {}", x); }
    fn foo(x: f64) { println!("f64: {}", x); }
}
```
 
and needs to produce a trait, one `impl` per overload, and the splatted entry-point function. The pipeline is straightforward once you see it laid out:
 
The parsing side leans entirely on `syn`. The macro's input isn't a single item, it's a sequence of whole functions, so the first thing needed is a type that knows how to parse "as many functions as there are" out of a token stream:
 
```rust
struct OverloadInput {
    functions: Vec<ItemFn>,
}
 
impl Parse for OverloadInput {
    fn parse(input: ParseStream) -> Result<Self> {
        let mut functions = Vec::new();
        while !input.is_empty() {
            functions.push(input.parse::<ItemFn>()?);
        }
        Ok(OverloadInput { functions })
    }
}
```
 
From there, the macro derives a trait name from the shared function name (`foo` becomes `FooArgs`), then walks every overload and, for each one, walks its arguments to build up three parallel lists: the argument types (which become the tuple the trait is implemented for), the argument names (which get rebound from the tuple inside the generated body), and the tuple-index expressions used to pull each value back out of `self`:
 
```rust
for func in &functions {
    let mut arg_types = Vec::new();
    let mut arg_names = Vec::new();
    let mut arg_indices = Vec::new();
    let block = &func.block;
 
    for (i, arg) in func.sig.inputs.iter().enumerate() {
        if let FnArg::Typed(pat_type) = arg {
            let ty = &pat_type.ty;
            arg_types.push(quote! { #ty });
 
            let arg_name = if let Pat::Ident(pat_ident) = &*pat_type.pat {
                let ident = &pat_ident.ident;
                quote! { #ident }
            } else {
                quote! { _arg }
            };
            arg_names.push(arg_name);
 
            let index = syn::Index::from(i);
            arg_indices.push(quote! { self.#index });
        }
    }
 
    impls.push(quote! {
        impl #trait_name for (#(#arg_types),*,) {
            fn call(self) {
                #(let #arg_names = #arg_indices;)*
                #block
            }
        }
    });
}
```
 
That last `quote!` block is doing the actual work: for a two-argument overload it produces `impl FooArgs for (i32, f64) { fn call(self) { let x = self.0; let y = self.1; <original body> } }`, reusing the function's own body verbatim rather than re-deriving it. Once every overload has produced its `impl`, the whole thing is assembled into the trait definition plus the splatted function:
 
```rust
let generated = quote! {
    trait #trait_name: std::marker::Tuple {
        fn call(self);
    }
 
    #(#impls)*
 
    fn #fn_name<T: #trait_name>(#[splat] args: T) {
        args.call()
    }
};
 
generated.into()
```
 
Getting this right was not a straight line. More than once, restructuring the loop that builds up `impls` left me with a stray or missing closing brace that put `let generated = quote! { ... }` outside the `overload` function entirely. The compiler's actual error for that, `` `let` cannot be used for global variables ``, doesn't point at "you have a brace in the wrong place" at all, so tracing it back to the real structural issue meant carefully re-reading the function from the top down rather than trusting the error location. It was a good reminder that in code that generates code, a small structural slip doesn't just produce a logic bug, it produces code that looks plausible and only reveals itself as wrong several layers downstream.
 
This work is split across two pull requests in the new repository my mentor created for it, `rustfoundation/overloading-macros`, kept deliberately separate from the standard library since there are many valid ways this macro could eventually be designed.
 
**[PR #5](https://github.com/rustfoundation/overloading-macros/pull/5)** covers the first working version of the macro: multiple overloaded free functions, each with a single argument, along with the tests proving each overload dispatches to the correct implementation.
 
**[PR #6](https://github.com/rustfoundation/overloading-macros/pull/6)** extends the macro to handle multiple arguments per function, of mixed types, with matching tests. By the end of it the macro correctly handled input like this:
 
```rust
overload! {
    fn foo(x: i32, y: f64) { println!("i32: {}, f64: {}", x, y); }
    fn foo(x: bool, y: i32, z: f64) { println!("bool: {}, i32: {}, f64: {}", x, y, z); }
}
 
foo(42i32, 3.14f64);
foo(true, 42i32, 3.14f64);
```
 
#6 also includes the project infrastructure needed to make the crate usable and its examples verifiable rather than just compilable: a README section explaining how to run the examples locally, and a CI workflow that actually executes each example binary. Since Cargo doesn't have a simple built-in way to discover binary names automatically, my mentor and I settled on maintaining an explicit list of binaries in the workflow, a small amount of manual upkeep in exchange for CI results that are easy to reason about.
 
Setting up the crate itself also meant learning some Cargo workspace mechanics I hadn't used before: shared dependency versions (`syn`, `quote`, `proc-macro2`) defined once at the workspace level and inherited by the macro crate via `.workspace = true`, and fixing a `rust-version` mismatch by pinning the workspace to nightly with `rust-toolchain.toml`, since `#[splat]` and `#[feature(tuple_trait)]` both require it.
 
### What's next
 
The next milestone is return values. This is a bigger design change than it first appears, because different overloads can return different types:
 
```rust
overload! {
    fn foo(x: i32) -> i32 { x * 2 }
    fn foo(x: f64) -> f64 { x * 2.0 }
}
```
 
which means the generated trait needs an associated type, `type Output; fn call(self) -> Self::Output;`, rather than a fixed return type. After that, the plan is to extend the macro to handle methods (functions taking `self`), and eventually generics.
 
---
 
## What Remains
 
With roughly a month left, my priorities are:
 
- Finish return value support in `overload!`
- Extend the macro to handle methods, not just free functions
- Continue writing `#[splat]` test coverage as new edge cases are discovered
- Document the macro properly so it is usable by people outside this project
This internship has moved from reading and testing someone else's compiler feature to actively designing and building a tool on top of it, which has been the most rewarding part of the experience so far.
 

## A Note of Gratitude

To my mentor teor, the Rust Foundation team, and the Outreachy organisers: thank you for creating a programme that made it possible for someone like me to get here. I do not take that lightly.

