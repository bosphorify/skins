# CLAUDE.md — working in `bosphorify-skins`

Operating manual for an agent **editing the skins** here. (Consumer install docs
are in [README.md](README.md).)

## What this repo is

The **canonical, themes-only** home for 15 shadcn/ui concept skins, distributed
as a shadcn registry. **No app, no components, no build framework** — just:

```
skins/*.css      the themes — one self-contained file per concept
r/*.json         the BUILT registry (shadcn add reads these; CSS is INLINED)
registry.json    registry manifest (one item per skin)
package.json     `pnpm build:registry` → regenerates r/
```

The live **demo/showcase** is a *separate* Next.js app in the sibling
`shadcn-xp` repo. That repo has its own copy of these skins which **may drift** —
**this repo is the source of truth.** There is no app here to preview in; to
eyeball a change, run the `shadcn-xp` showcase or drop the CSS into any shadcn
app and set `data-skin`.

## How a skin works

A skin is one CSS file, `skins/<name>.css`, that does two things, both scoped to
`[data-skin="<name>"]` so swapping `data-skin` on `<html>` swaps the whole look:

1. **Tokens** — `:root[data-skin="<name>"] { --background: …; --primary: …; … }`.
   Use the **`:root[data-skin="x"]`** prefix (not bare `[data-skin="x"]`) so it
   outranks the consumer's core `:root` defaults.
2. **Shape/ornament** — unlayered `[data-skin="<name>"] [data-slot="button|card|…"]`
   rules (bevels, chrome, shadows, radii). Unlayered rules outrank Tailwind v4's
   `utilities` cascade layer, so they win **without `!important`**. Buttons key
   off `.group/button` (it survives Radix `asChild` className merges; `data-slot`
   does not on buttons).

The components themselves are never modified — these are pure CSS overlays over
stable `data-slot` / `data-state` hooks.

## The workflow (do this every time)

1. Edit or add `skins/<name>.css`. **Adding a new skin?** also add an item to
   `registry.json` (`"files": [{ "path": "skins/<name>.css", "target":
   "app/skins/<name>.css" }]`).
2. **Rebuild the registry** — `r/*.json` **inline the CSS**, so they go stale the
   moment you touch a skin:
   ```bash
   pnpm build:registry        # = shadcn build --output r
   ```
3. `git commit` + `git push` (origin → `bosphorify/skins`, branch `master`).
4. Consumers only update by **re-running** `shadcn add … --overwrite`. An install
   is a **copy, not a live link** — a rebuild on the consumer side does *not*
   re-fetch. Don't expect their `next build` to pick anything up.

## Hard rules / gotchas (each one already bit us once)

- **Never put `@import` inside a skin CSS file.** A stray `@import` (e.g. a
  Google-Fonts line) breaks the bundled registry build — it 500'd every skin
  once. Webfonts belong in the consuming app's `<head>`, not the skin file.
- **Rebuild the registry after *every* skin edit** (step 2). Forgetting this is
  the #1 way a "fixed" skin ships unchanged.
- **`color-mix` / `rgba` channel grammar:** `rgba(var(--x), .3)` needs
  **comma**-separated channels (`--x: 1, 2, 3`); `rgb(var(--x) / .3)` needs
  **space** channels (`--x: 1 2 3`). Mixing them makes the value invalid and the
  property silently drops (this flattened *all* of clay's shadows to `none`).
- **Custom-property substitution:** a composed prop declared at `:root`
  (e.g. `--neu-raised-lg` built from `var(--neu-elevate)`) substitutes its inner
  `var()`s at the `:root` computed-value stage. Overriding the inner var on a
  descendant (a variant) does **not** retarget the composed prop — you must
  **re-declare the whole composed prop locally** in that rule. (Broke
  neumorphic's destructive button/badge.)
- **A "unit" must be a real CSS unit.** A display-only multiplier label like
  `"x"` must never be appended to the value — `--neu-elevate: 3x` makes
  `calc(6px * 3x)` invalid and drops the shadow entirely. (This lives in the
  `shadcn-xp` editor, but the same trap applies to any tooling that writes these
  vars.)
- **Keep text legible on every skin.** Pair text colors with the skin's own
  `--card` / `--card-foreground` / `--muted-foreground` (those pairs are
  guaranteed readable because components depend on them) rather than hardcoding a
  color that fails contrast on some surface.
- **Substring selectors collide:** `[class*="destructive"]` also matches stock
  `aria-invalid:ring-destructive/…` classes — match the specific utility
  (`[class*="bg-destructive"]`) instead.
- **Focus rings:** an equal-specificity variant `box-shadow` can silently
  overwrite a `box-shadow`-based ring — prefer `outline`-based focus rings.

## Quality bar

All 15 skins were certified ≥95/100 by adversarial subagent critics on
configurability / visuals / design / naming-authenticity. Keep that bar: a skin
should read unmistakably as its concept (XP looks like real XP), stay fully
token-tunable, and pass WCAG AA contrast on its own surfaces.
