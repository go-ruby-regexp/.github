<p align="center"><img src="https://raw.githubusercontent.com/go-ruby-regexp/brand/main/social/go-ruby-regexp.png" alt="go-ruby-regexp" width="640"></p>

<h1 align="center">go-ruby-regexp</h1>
<p align="center"><strong>Onigmo in pure Go — Ruby's regexp engine, with the features RE2 leaves out, no cgo.</strong></p>

<p align="center">
  🌐 <a href="https://go-ruby-regexp.github.io">Website</a> ·
  📚 <a href="https://go-ruby-regexp.github.io/docs/">Documentation</a>
</p>

<p align="center">
  <a href="https://go-ruby-regexp.github.io/docs/"><img alt="Docs" src="https://img.shields.io/badge/docs-mkdocs--material-9B1C2E?style=flat-square"></a>
  <a href="https://github.com/go-ruby-regexp/regexp/blob/main/LICENSE"><img alt="License: BSD-3-Clause" src="https://img.shields.io/badge/license-BSD--3--Clause-blue?style=flat-square"></a>
  <img alt="Go 1.26.4+" src="https://img.shields.io/badge/go-1.26.4%2B-00ADD8?style=flat-square&logo=go&logoColor=white">
  <img alt="Engine roadmap: Phases 0–4 complete" src="https://img.shields.io/badge/phase-0--4%20complete-1a7f37?style=flat-square">
</p>

---

go-ruby-regexp is a **pure-Go (no cgo) reimplementation of [Onigmo](https://github.com/k-takata/Onigmo)**,
the regular-expression engine used by Ruby. Go's standard `regexp` is RE2 —
linear-time, but deliberately without **backreferences** or **lookaround**, and
with different (leftmost-longest) match semantics. A byte-compatible Ruby regexp
therefore needs a **backtracking engine**, and that is what go-ruby-regexp is: a
faithful backtracking VM supporting backreferences, lookahead/lookbehind,
possessive quantifiers, atomic groups, named groups, subexpression calls, and
Ruby's leftmost-first semantics — hardened against catastrophic backtracking
(ReDoS) with **memoization and a deterministic time/step budget** (as Ruby ≥3.2).

It is **standalone and reusable** by any Go program, and is the regexp backend
for the sibling org
[go-embedded-ruby](https://github.com/go-embedded-ruby/ruby).

## Repositories

| Repo | What it is |
|------|------------|
| [**regexp**](https://github.com/go-ruby-regexp/regexp) | the engine: `syntax`/`compile`/`vm`/`charset`/`encoding` + the public `regexp` API |
| [**docs**](https://github.com/go-ruby-regexp/docs) | MkDocs Material documentation, versioned with [mike], served at [/docs/](https://go-ruby-regexp.github.io/docs/) |
| [**go-ruby-regexp.github.io**](https://github.com/go-ruby-regexp/go-ruby-regexp.github.io) | the Hugo landing page |
| [**brand**](https://github.com/go-ruby-regexp/brand) | logos and brand assets |

## Principles

- **Pure Go, zero cgo.** Cross-compiles and embeds anywhere; a static binary by
  default.
- **Backtracking VM, not RE2.** Backreferences, lookaround and leftmost-first
  semantics require it — that is precisely what makes go-ruby-regexp Ruby-compatible
  where the standard library cannot be.
- **ReDoS-hardened.** Memoization plus a deterministic timeout / backtrack-step
  budget that always terminates — never a host watchdog alone.
- **Differentially tested** against Onigmo/MRI; behaviour is byte-for-byte.
- **Standalone & reusable.** No dependency on the Ruby runtime.
- **100% test coverage** is the target, enforced as a CI gate.

## Status

**Engine roadmap (Phases 0–4) complete.** A backtracking VM with leftmost-first
semantics covering literals/escapes, `.`, character classes (incl. POSIX
`[[:alpha:]]` and multibyte members `[é]` / `[à-ï]`), anchors (`\A \z \Z \G ^ $`),
**every quantifier mode** (greedy, lazy `*? +? ??`, possessive `*+ ++ ?+`, atomic
groups `(?>…)`), capturing/non-capturing/named groups, alternation,
**backreferences `\1` / `\k<name>`**, **lookahead and fixed/bounded-width
lookbehind**, **recursive subexpression calls `\g<…>`**, inline flags `(?imx)`,
**`\p{…}` Unicode properties**, rune-level `/i` case folding, `\h` / `\H`, `\R`,
and **UTF-8 / ASCII-8BIT multi-encoding** with a char-advancing `.`. It is
ReDoS-hardened (memoization + step budget + recursion cap + `WithTimeout`) and
carries a transparent **start-position / interior-literal optimizer** (up to
~210× faster) with a benchmark suite — differential-tested against MRI, 100%
coverage, CI green across 6 arches. Phase 5 (the Ruby `Regexp`/`MatchData`
surface and replacement DSL) is downstream in the go-embedded-ruby adapter that
consumes this engine, not part of this module. The architecture, the documented
out-of-scope boundaries, and the six-phase roadmap live in
[regexp/docs/plan-regexp.md](https://github.com/go-ruby-regexp/regexp/blob/main/docs/plan-regexp.md)
and on the [documentation site](https://go-ruby-regexp.github.io/docs/).

BSD-3-Clause.

[mike]: https://github.com/jimporter/mike
