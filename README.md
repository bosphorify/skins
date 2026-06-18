# bosphorify-skins

**15 swappable concept skins for [shadcn/ui](https://ui.shadcn.com)** — Windows
XP, Brutalist, Glass, Terminal, Synthwave and more — distributed as a shadcn
registry. Each skin is **one CSS file** scoped to `[data-skin="<name>"]`; it
layers over your **unmodified** shadcn components (no forks, no component
changes). Flip `data-skin` on `<html>` and everything repaints.

This repo is **themes only** — just the skin CSS and the installable registry.
(The live demo / showcase app lives separately in `shadcn-xp`.)

## Use a skin in your app

**1. Install** — writes `app/skins/xp.css`, your components stay untouched:

```bash
npx shadcn@latest add https://raw.githubusercontent.com/bosphorify/skins/master/r/skin-xp.json
```

**2. Import** it in your global stylesheet (e.g. `app/globals.css`):

```css
@import "./skins/xp.css";
```

**3. Activate** by setting `data-skin` on the root `<html>` (in Next.js, the
`<html>` tag in `app/layout.tsx`):

```html
<html lang="en" data-skin="xp">
```

Every shadcn component now renders in the **XP** skin. To use another concept,
change **both** the URL's `skin-<name>` and the `data-skin` value. Switch at
runtime by setting `document.documentElement.dataset.skin = "terminal"`.

> The raw-GitHub URL above works as soon as this repo is public. Until then, the
> identical registry is also live from the showcase repo:
> `https://raw.githubusercontent.com/mtahasylmz/shadcn-xp/master/public/r/skin-xp.json`

### Install several / drive it from an agent

Register the namespace once in your project's `components.json`:

```jsonc
{
  "registries": {
    "@skins": "https://raw.githubusercontent.com/bosphorify/skins/master/r/{name}.json"
  }
}
```
```bash
npx shadcn@latest add @skins/skin-terminal
```

Point the **shadcn MCP server** at the same `@skins` registry and an agent can
browse and apply these skins by name while it builds an app.

## Skins

| `skin-<name>` (install) · `data-skin` (activate) | Concept |
| --- | --- |
| `base`       | Concept-free, fully tunable structural dials (no skin at all)    |
| `xp`         | Windows XP "Luna" — beveled putty controls, navy title gradient  |
| `win98`      | Windows 98 — grey 4-edge 3D bevels, navy title bars              |
| `aqua`       | Mac OS X Aqua — glossy gel lozenges, pinstripes, candy gradients |
| `macos`      | Modern macOS — vibrancy blur, hairline borders, traffic lights   |
| `material`   | Material Design 3 — tonal surfaces, state layers, elevation      |
| `brutalist`  | Hard black borders, chunky offset shadows, uppercase grotesk     |
| `neumorphic` | Monochrome soft-UI, extruded via paired light/dark shadows       |
| `clay`       | Claymorphism — puffy inflated 3D, pastel palette, big radii      |
| `glass`      | Frosted translucent panels, backdrop blur, over a vivid gradient |
| `terminal`   | Phosphor green-on-black, monospace, `[ bracketed ]`, scanlines   |
| `cyberpunk`  | Neon cyan/magenta glow, clipped angular corners, dark HUD        |
| `pixel`      | 8-bit — stepped pixel bevels, Press Start 2P, CRT scanlines      |
| `synthwave`  | Outrun — sunset gradient, neon glow, perspective grid horizon    |
| `editorial`  | Print/editorial — ink on cream, serif hierarchy, hairline rules  |
| `swiss`      | Swiss International — Helvetica grid, signal-red accent           |

Plus `theme-xp` — the **tokens-only** XP theme (a `registry:theme`, colors /
radius / font, no structural overlay) if you only want the palette.

## Layout

```
skins/*.css        the themes — one drop-in file per concept ([data-skin]-scoped)
r/*.json           built shadcn registry (CSS inlined, ready to serve)
registry.json      registry manifest
```

## Editing a skin

1. Edit `skins/<name>.css`.
2. Rebuild the registry so the served JSON picks up the change (it **inlines**
   the CSS, so it goes stale otherwise):
   ```bash
   pnpm build:registry      # → regenerates r/*.json
   ```
3. Commit + push. Consumers pick it up by **re-running** `shadcn add … --overwrite`
   — installs are a copy, not a live link, so a rebuild on the consumer side does
   not auto-update.

## License

MIT.
