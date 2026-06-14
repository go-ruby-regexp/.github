<p align="center"><img src="https://raw.githubusercontent.com/go-onigmo/brand/main/social/go-onigmo.png" alt="go-onigmo" width="640"></p>

<h1 align="center">go-onigmo</h1>
<p align="center"><strong>Onigmo in pure Go — Ruby's regexp engine, with the features RE2 leaves out, no cgo.</strong></p>

<p align="center">
  🌐 <a href="https://go-onigmo.github.io">Website</a> ·
  📚 <a href="https://go-onigmo.github.io/docs/">Documentation</a>
</p>

<p align="center">
  <a href="https://go-onigmo.github.io/docs/"><img alt="Docs" src="https://img.shields.io/badge/docs-mkdocs--material-9B1C2E?style=flat-square"></a>
  <a href="https://github.com/go-onigmo/regexp/blob/main/LICENSE"><img alt="License: BSD-3-Clause" src="https://img.shields.io/badge/license-BSD--3--Clause-blue?style=flat-square"></a>
  <img alt="Go 1.26.4+" src="https://img.shields.io/badge/go-1.26.4%2B-00ADD8?style=flat-square&logo=go&logoColor=white">
  <img alt="Phase 0+1 done" src="https://img.shields.io/badge/phase-0%2B1%20done-1a7f37?style=flat-square">
</p>

---

go-onigmo is a **pure-Go (no cgo) reimplementation of [Onigmo](https://github.com/k-takata/Onigmo)**,
the regular-expression engine used by Ruby. Go's standard `regexp` is RE2 —
linear-time, but deliberately without **backreferences** or **lookaround**, and
with different (leftmost-longest) match semantics. A byte-compatible Ruby regexp
therefore needs a **backtracking engine**, and that is what go-onigmo is: a
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
| [**regexp**](https://github.com/go-onigmo/regexp) | the engine: `syntax`/`compile`/`vm`/`charset`/`encoding` + the public `regexp` API |
| [**docs**](https://github.com/go-onigmo/docs) | MkDocs Material documentation, versioned with [mike], served at [/docs/](https://go-onigmo.github.io/docs/) |
| [**go-onigmo.github.io**](https://github.com/go-onigmo/go-onigmo.github.io) | the Hugo landing page |
| [**brand**](https://github.com/go-onigmo/brand) | logos and brand assets |

## Principles

- **Pure Go, zero cgo.** Cross-compiles and embeds anywhere; a static binary by
  default.
- **Backtracking VM, not RE2.** Backreferences, lookaround and leftmost-first
  semantics require it — that is precisely what makes go-onigmo Ruby-compatible
  where the standard library cannot be.
- **ReDoS-hardened.** Memoization plus a deterministic timeout / backtrack-step
  budget that always terminates — never a host watchdog alone.
- **Differentially tested** against Onigmo/MRI; behaviour is byte-for-byte.
- **Standalone & reusable.** No dependency on the Ruby runtime.
- **100% test coverage** is the target, enforced as a CI gate.

## Status

**Phases 0 and 1 done.** A greedy backtracking VM with leftmost-first semantics:
literals/escapes, `.`, character classes, anchors (`\A \z \Z ^ $`), greedy
quantifiers, capturing/non-capturing groups, alternation, **named groups
`(?<name>…)` and backreferences `\1` / `\k<name>`** — differential-tested against
MRI, 100% coverage, CI green across 6 arches. Lookaround, Unicode `\p{}`,
case-folding and ReDoS memoization are next. The architecture and six-phase
roadmap live in
[regexp/docs/plan-regexp.md](https://github.com/go-onigmo/regexp/blob/main/docs/plan-regexp.md)
and on the [documentation site](https://go-onigmo.github.io/docs/).

BSD-3-Clause.

[mike]: https://github.com/jimporter/mike
