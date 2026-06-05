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

## A Note of Gratitude

To my mentor teor, the Rust Foundation team, and the Outreachy organisers: thank you for creating a programme that made it possible for someone like me to get here. I do not take that lightly.

