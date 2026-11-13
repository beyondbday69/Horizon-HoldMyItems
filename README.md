# Hold My Items — Horizon native port

A native Rust port of [Hold My Items](https://modrinth.com/mod/hold-my-items) 5.1.1's first-person
hand and item animation engine, by way of
[iuaititi's Horizon port](https://github.com/iuaiti/Hold-my-items-horizon), built as a **feature of
the Horizon Android client** rather than as a standalone mod.

First-person arms and items that move like an extension of your body: swings, walking, sneaking,
eating, climbing, and per-item poses.

> **Licence: MIT.** The Horizon port this derives from is MIT, Copyright (c) 2026 iuaititi. The
> upstream mod's Lua scripts are CC0-1.0. See [Licence](#licence).

---

## What this repo is

**Horizon mod source**, in the layout the launcher expects:

| Path | What it is |
|---|---|
| `horizon-native.json` | The manifest: `schema`, `id`, `version`, `game`, `horizon`, `mixins`, `hooks` |
| `mixins/holdmyitems/` | The mixin and hook sources, plus `horizon-mixin.toml` and the Lua/item data packs |
| `hold_my_items.hnmod` | The packaged bundle (release asset), built from the two above |

There is no `horizon-compiled.json` here on purpose — see below.

### `horizon-compiled.json` is build output, not source

Horizon generates it when it compiles a bundle from mixin sources. Committing it makes the
launcher reject the bundle:

```
unexpected native source: horizon-compiled.json
```

It is in `.gitignore`, and the release asset does not carry it. If you rebuild the bundle
yourself, leave it out.

### This is a real mod, not a marker

An earlier 5.1.1 release of this repo shipped a ~1 KB marker bundle — a manifest plus a
`src/lib.rs` stub with only a title and description — which declared the feature but carried no
actual mod code. That is why the launcher refused it. This 5.1.2 bundle carries the full mixin
sources (22 files, ~830 KB), which is what a launcher can install and boot.

Rebuild the release asset with:

```sh
python3 -c "
import zipfile, os
files = ['horizon-native.json'] + sorted(
    os.path.join(r, n).replace('./', '')
    for r, _, ns in os.walk('mixins') for n in ns)
with zipfile.ZipFile('hold_my_items.hnmod', 'w', zipfile.ZIP_DEFLATED) as z:
    for f in files:
        z.write(f, f)
"
```

## Requirements

- Horizon Android, `hooks` API **415** or later (the manifest's `hooks` field)
- Android, arm64-v8a
- Enable it after installing: **Options → Mods → Hold My Items → enable** (mods are off by default)

## Settings

All under *Options → Mods → Hold My Items*, in this order. Tap a row to step it; settings are
written to `config.json` in the mod's data directory and survive a restart.

| Row | Range | Default |
|---|---|---|
| 3D Held Blocks | on / off | on |
| Hand Glow & Petal Particles | on / off | on |
| Dynamic Hand Lighting | on / off | on |
| Eat/Drink/Walk Camera Shake | on / off | on |
| Hand Switch & Bell Sounds | on / off | on |
| Alternating Sword Combos | on / off | on |
| Base Swing Speed | 4–24 ticks | 10 |
| Viewmodel Offset | X/Y/Z, each `-0.50`–`+0.50` | 0.00 |
| Reset Hold My Items Defaults | action | — |

If `config.json` has a syntax error, the panel shows a single **Reload config.json** row instead so
you can fix it without a restart.

There is also an inspect key, **C** by default, bound to *Inspect Held Item*.

## What it does

- **Swings** that follow the shape of the held item rather than a single generic arc
- **Per-item poses and motion** — tools, swords, blocks, food, bows, shields and more
- **Locomotion**: walking, sneaking, climbing, and eating all move the hand
- Camera shake, hand particles and dynamic lights as separate toggles
- 3D rendering for held blocks instead of a flat sprite

The animation maths are ported; the injector, the render takeover and the resource pack of the
original Horizon port are not. This runs as a native feature, not a script injection.

## How it fits into Horizon

`horizon-mixin.toml` lists two modules and thirteen injection points: `runtime` becomes
`crate::game::holdmyitems` and `native` becomes `crate::game::holdmyitems_native`, with the
injections hooking the hand render, swing, tick, frame, HUD, settings and key paths.

The mod pins `horizon_revision` `c9d474dd9116e84c243f8992d75c11314df56f06`, which is the revision
the mixin sources were written against. The manifest's `horizon` field carries that same revision,
because the launcher checks the two agree.

## Licence

**MIT**, Copyright (c) 2026 **iuaititi**. See
[`hold-my-items-core/LICENSE`](hold-my-items-core/LICENSE).

Credits:

- Original mod: the Hold My Items authors
- Horizon port, which this is based on: **iuaititi**
- Horizon native adaptation: **beyondbday69**

The upstream mod's Lua scripts are CC0-1.0. Only the animation maths are ported here.

Horizon is not affiliated with the Hold My Items authors or with iuaititi.
Not affiliated with Mojang or Microsoft.
