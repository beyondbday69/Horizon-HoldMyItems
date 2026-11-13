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

Two things:

| Path | What it is |
|---|---|
| `hold-my-items-core/` | The animation engine, game-free, buildable and testable on its own |
| `hold_my_items.hnmod` | The bundle that declares the feature to Horizon |

### The `.hnmod` is not a working mod by itself

`hold_my_items.hnmod` is a ~1 KB **marker**. It carries `horizon-native.json`,
`horizon-compiled.json` and a `src/lib.rs` stub whose export is a title and a description. It
registers the feature id `hold_my_items` with Horizon so the game turns the code on and shows a row
under *Options → Mods*.

The code that actually animates and draws the hand is **not** in the bundle. It is compiled into
the Horizon client itself, in `crates/hold-my-items-core/` (mirrored here) and
`crates/horizon-client/src/game/hold_my_items.rs` (the adapter that drives it from the game).

So: installing the `.hnmod` on a stock Horizon build does nothing. It only means something in a
client that already contains this feature. It is published so the feature is visibly declared and
versioned, not because dropping it in adds animation.

## Requirements

- A **Horizon Android dev build** with this feature compiled in
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

## Build and test

```sh
cargo test
```

Five unit tests cover the engine's maths. Nothing else in this repo builds on its own — the
adapter needs the Horizon client.

## How it fits into Horizon

The feature is gated the same way a bundled mod is: it runs only when `native_on("hold_my_items")`
is true and the loaded bundle lists the `hold_my_items` feature.

The engine is deliberately game-free. Everything that needs the game — reading the item in hand,
the player's state, the camera, the frame clock — goes through a narrow set of inputs the adapter
supplies, which is why the engine has real tests instead of only compiling.

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
