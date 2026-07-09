# kotoba-lang/scene2d

Kotoba package for `kotoba.scene2d`.

## Dependency change (2026-07-09)

`kotoba.scene2d` now requires `kami.sprite-gpu` (from `kotoba-lang/webgpu`) instead of the
standalone `kotoba-lang/sprite-gpu` repo's `kotoba.sprite-gpu`. `kotoba-lang/sprite-gpu`'s copy of
that namespace had diverged from `kami.sprite-gpu` (which every live consumer actually depends on)
and was missing a real bug fix (`:rect` primitives rendered ~2x too large — see
`kotoba-lang/webgpu`'s CHANGELOG.md / `kotoba-lang/sprite-gpu`'s CHANGELOG.md for the full
writeup). `kotoba-lang/sprite-gpu` itself now re-exports `kami.sprite-gpu` too, so this change is
behaviour-preserving either way — it just removes an extra hop and gets the fix directly.

**Known pre-existing, unrelated issue found while touching this repo:** `deps.edn` depends on
`io.github.kotoba-lang/text {:local/root "../text"}` and `kotoba.scene2d` requires
`[kotoba.text :as txt]`, but the `kotoba-lang/text` repo only defines `kotoba.lang.text` (a
string/regex/unicode helpers namespace, unrelated to `kotoba.scene2d`'s 7-segment-glyph text
renderer) — there is no `kotoba.text` namespace anywhere in `kotoba-lang/text`. `clojure -M:test`
fails at this `require` on a clean checkout, before and independently of the sprite-gpu change
above. This looks like the same "restore left main empty for one repo but not its sibling" failure
mode documented for `sprite-gpu`/`gpu`/`webgl`, but for `text` — likely `kami.text` (which does
exist, in `kotoba-lang/webgpu`) never got a standalone-repo equivalent restored. Left unfixed here:
it's a separate repo/namespace than the sprite-gpu/gpu/webgl duplication this change addresses,
and fixing it means deciding what `kotoba-lang/text` *should* contain, which is out of scope for a
dependency-dedup pass.

## Test

```sh
clojure -M:test
```
