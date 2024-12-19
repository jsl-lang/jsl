# JSL Language

```c
abstract:item type="item" {
    size 0.2 1 3
    model "madel/cube.obj"
    texture "texture/missing.png"
    material "material/missing.mtl"
    pointlessProp "i am prop"
    transform {
        1 0 0 0
        0 1 0 0
        0 0 1 0
        0 0 0 1
    }
    glow on
}

override abstract:item {
    override model "model/cube.obj"
}

override abstract:item {
    delete pointlessProp
}

armor inherit=abstract:item type="pickup" interactable
```

- [Specifications](/SCHEMA-SPEC.md)
- [Implementations](#implementations)
- [FAQ](#faq)
- [Discussions](https://github.com/jsl-lang/jsl/discussions)

## Design
Based on [SDLang](https://sdlang.org) and partially [KDL](https://kdl.dev) out of need for new features.

JSL takes SDLang's base specification and adds on top of it without loosing support for most of files used, while at the same time drawing inspiration from KDL's features, like `/-` comments and allowing comments after line escaping with `\`.

New features added by JSL (might not be exhaustive list):
- [spec](SCHEMA-SPEC.md#properties) Automatic truthfulness of properties (`node property` = `node property=true`).
- [spec](SCHEMA-SPEC.md#properties) Automatic falseness of properties (`node !property` = `node property=false`).
- [spec](SCHEMA-SPEC.md#literal-strings) Literal strings (`node prop=literal:string` = `node prop="literal:string"`).
- [spec](SCHEMA-SPEC.md#escape-sequences) More escape sequences (contains all escapes supported by D).
- [spec](SCHEMA-SPEC.md#multi-line) Node `/-` comments.
- [spec](SCHEMA-SPEC.md#line-continuation) Comments after line break with `\` are allowed (even in strings).
- [spec](SCHEMA-SPEC.md#multi-line) Nesting multiline comments with `/+ comment +/` (`/* comment */` comments are not nesting).
- [spec](SCHEMA-SPEC.md#date-and-time) New date/time types (including ISO standard).
- [spec](SCHEMA-SPEC.md#numbers) Exponential numbers.


## Implementations

Since JSL is based on both SDLang and KDL v1, which is itself based on SDLang, JSL can be partially used with any of SDLang's or KDL's tooling.

For now there will be lists of forks of SDLang's and KDL's implementations and editor support with indication from which language it came from. Please note that since JSL is intended to be compatible with SDLang, SDLang's tooling is more preferred, until there are more "native" JSL implementations.

TLDR:
- Everything under `Forks` might not parse JSL files.
- `SDLang` based forks will parse JSL better.
- There are more `KDL` based forks, but they support way less features. 

### JSL

WIP

### Forks

SDLang based:
- [java](https://github.com/jsl-lang/jsl-java-sdl)
- D
    - [sdlite](https://github.com/jsl-lang/jsl-d-lite-sdl)
    - [sdlang-d](https://github.com/jsl-lang/jsl-d-sdl)
    - [sdlangp](https://github.com/jsl-lang/jsl-d-pegged-sdl)
- [dart](https://github.com/jsl-lang/jsl-dart-sdl)
- [dotnet](https://github.com/jsl-lang/jsl-dotnet-sdl)
- [rust](https://github.com/jsl-lang/jsl-rust-sdl)

KDL based:
- [c](https://github.com/jsl-lang/jsl-c-kdl)
- [ruby](https://github.com/jsl-lang/jsl-ruby-kdl)
- Go
    - [kdlgo](https://github.com/jsl-lang/jsl-go-kdlgo-kdl)
    - [gokdl](https://github.com/jsl-lang/jsl-go-gokdl-kdl)
- [crystal](https://github.com/jsl-lang/jsl-crystal-kdl)
- [dart](https://github.com/jsl-lang/jsl-dart-kdl)
- [swift](https://github.com/jsl-lang/jsl-swift-kdl)
- [lua](https://github.com/jsl-lang/jsl-lua-kdl)
- Rust
    - [kdl-rs](https://github.com/jsl-lang/jsl-rust-kdl)
    - [knuffel](https://github.com/jsl-lang/jsl-rust-knuffel-kdl)
    - [kaydle](https://github.com/jsl-lang/jsl-rust-kaydle-kdl)
- [ocaml](https://github.com/jsl-lang/jsl-ocaml-kdl)
- [java](https://github.com/jsl-lang/jsl-java-kdl)
- [elixir](https://github.com/jsl-lang/jsl-elixir-kdl)
- Python
    - [kdlpy](https://github.com/jsl-lang/jsl-python-kdlpy-kdl)
    - [cuddle](https://github.com/jsl-lang/jsl-python-cuddle-kdl)
- [nim](https://github.com/jsl-lang/jsl-nim-kdl)
- [javascript](https://github.com/jsl-lang/jsl-javascript-kdl)
- [php](https://github.com/jsl-lang/jsl-php-kdl)
- [lisp](https://github.com/jsl-lang/jsl-lisp-kdl)
- [dotnet](https://github.com/jsl-lang/jsl-dotnet-kdl)
- [haskell](https://github.com/jsl-lang/jsl-haskell-kdl)

## Editor Support

### JSL

- [vim](https://github.com/jsl-lang/jsl.vim)

### Forks

SDLang based:
- [emacs](https://github.com/jsl-lang/jsl-emacs-sdl)
- [sublime](https://github.com/jsl-lang/jsl-sublime-sdl)

KDL based:
- [vscode](https://github.com/jsl-lang/jsl-vscode-kdl)
- [treesitter](https://github.com/jsl-lang/tree-sitter-jsl-kdl)
- [intellij](https://github.com/jsl-lang/jsl-intellij-kdl)
- [sublime](https://github.com/jsl-lang/jsl-sublime-kdl)
- [vim](https://github.com/jsl-lang/jsl-vim-kdl)

## Overview

### Basics

### Literals

### Misc

## FAQ

#### JSL name?

It's just something that was available, there's no meaning behind it. 

#### Why another language (14? Ridiculous!)?

See next answer.

#### Why not SDLang or KDL?

KDL is a very good and very supported language, but it made too many modifications to original SDLang specification.

SDLang, however, is very fitting for almost any needs, but! There are certain features that SDLang is missing because of strictness it decided to enforce. JSL simplifies some of those features and adds new ones.

For new features that JSL adds see the [design](#design) part of readme.

JSL is a just an extension to SDLang and JSL tools will be able to be used with SDLang without any problem.

#### YAML?

Not flexible enough.

#### JSON?

Not intended for humans.

#### TOML?

It's great, but it has same problems as good old INI.

#### XML?

Have you ever seen HTML files?

## License

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

This license applies to the text and assets _in this repository_.
Implementations of this specification are not "derivative works", and thus are
not bound by the restrictions of CC-BY-SA.
