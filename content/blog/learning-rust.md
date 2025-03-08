+++
title = "Learning Rust and Building a CLI"
date = "2025-03-05"
description = "My experience learning Rust and building dotpatina"

[taxonomies]
tags = ["Software", "Rust", "AI"]

[extra]
local_image = ""
+++

Recently, I learned [Rust](https://www.rust-lang.org/) to build [dotpatina](https://github.com/axis7818/dotpatina). And along the way, I tried some AI tools too.

# A Software Engineer's Urge to Reinvent the Wheel

Lately, I have been feeling a particularly strong itch to learn a new technology and just build something. This feeling is nothing new to me, as I always have a desire to expand my software engineering toolbox and I am drawn to new technologies like a moth to a flame. So, I did what I do best and decided to build an over-engineered tool to solve a minor problem: managing system configuration and dotfiles.

Over the course of a day, I use 3 different computers. My work MacBook, my personal MacBook, and my Windows gaming PC. On each of these computers, I install and configure the same set of software (ex: git, zsh, tmux). In order to synchronize configuration across machines, I set up a [dotfiles repository](https://github.com/axis7818/dotfiles).

> TODO: image of 3 computers & dotfiles repository

{{ admonition(type="info", text="See [dotfiles.github.io](https://dotfiles.github.io) for more information on dotfiles repositories.") }}

With my dotfiles repository cloned onto each of my computers, the problem becomes copying files to their expected filesystem location for the respective tools to use. This can be managed via scripts, but these scripts get tricky when files need to be slightly different for each machine. For example, on my work laptop, I want my gitconfig email address to be my work address rather than my personal one.

I used this problem as an excuse to learn [Rust](https://www.rust-lang.org/) and build a CLI utility, [dotpatina](https://github.com/axis7818/dotpatina). Dotpatina allows me to [declaratively manage](https://github.com/axis7818/dotpatina?tab=readme-ov-file#patina-file) dotfiles using [handlebars templating](https://handlebarsjs.com/guide/) and renders [diff visualizations](https://github.com/axis7818/dotpatina?tab=readme-ov-file#applying-a-patina) to make it clear what files need to change when I update my dotfiles repository.

![patina apply gif](/img/dotpatina/update-patina.gif)

# Being a Beginner Again

I chose Rust primarily because I have never used it before. And, its [performance](https://niklas-heer.github.io/speed-comparison/) & [memory safety](https://www.nsa.gov/Press-Room/Press-Releases-Statements/Press-Release-View/Article/3608324/us-and-international-partners-issue-recommendations-to-secure-software-products/) guarantees sound like it would make a fun language for future projects.

My objective was to build a "production ready" (in quotes because I have no expectations that anyone other than me will use dotpatina) tool from the ground up using a new language and toolchain. I just wanted to be a beginner again, and Rust's famously steep learning curve seemed like the perfect choice.

> TODO: image of Rust learning curve

And indeed it was.

## Beginner Rust Resources

Before really building anything, I started with the general Rust tutorials.

1. [rust by example](https://doc.rust-lang.org/rust-by-example/): Very good comprehensive language overview
1. [rustlings](https://rustlings.cool/): Exercises to test Rust knowledge
1. [Learn X in Y Minutes (Where X = Rust)](https://learnxinyminutes.com/rust/): My personal favorite website for getting familiar with a new language

These were good introductions and served to introduce me to [cargo](https://doc.rust-lang.org/cargo/guide/why-cargo-exists.html), Rust's toolchain, and concepts (like [borrowing](https://doc.rust-lang.org/rust-by-example/scope/borrow.html) and [lifetimes](https://doc.rust-lang.org/rust-by-example/scope/lifetime.html)).

## Learning Rust with AI Assistance

AI is helpful as a tutor. Uses for AI when learning a language.

- explaining errors
- asking questions when you don't have the right vocabulary

However, it is easy to unknowingly generate tech debt.

It is important to study the generated code to understand what it does.

Automated testing is important for validating AI-generated code.

# Development Environment

## VSCode vs Rust Rover

- best test running & exploring
- best debugging
- integrated tools
  - documentation window

# Thoughts on Rust

## When it "Clicks", It Feels Great

## Consistent Ecosystem

## Modern but Hieroglyphic

# Takeaways

1. It is satisfying to build something for the sake of learning (like making your own game engine)
1. Rust is a great language
1. Using AI as a beginner vs expert
