# Earthline Defender

> **DEFEND EARTH. LOOK GOOD. DO IT AGAIN.**

A mobile-first, single-file, neon-arcade vertical rail shooter. Canvas-based, PWA-ready, no frameworks. Built to be wrapped as a TWA for Play Store distribution.

## Run

Open `index.html` in any modern browser, or serve the folder over HTTP for full PWA support:

```sh
python3 -m http.server 8080
# then visit http://localhost:8080 on a phone or with DevTools device mode
```

For PWA installability and service-worker caching, the site must be served over HTTP(S) — not opened as a `file://`.

## Controls

- **Drag** anywhere on the screen to move. The ship fires automatically while your thumb is down.
- **Double-tap** to deploy **Starshield** (when the meter is full).
- **Tap the Starshield bar** to deploy **Doom Squadron** when its meter is full.
- **Tap the green button** (bottom right) for **Planetary Yeet** — clears the screen, damages bosses.
- Keyboard: `WASD`/`Arrows` to move, `Space` to fire, `Y` Yeet, `E` Starshield, `D` Doom Squadron.

## Build phases (from spec)

- **Phase 1** — visual overhaul (scrolling starfield, neon HUD, Moore Gunns title screen), space octopus enemy, multi-track audio system.
- **Phase 2** — full enemy roster (10), boss system with phase thresholds, Planetary Yeet.
- **Phase 3** — Doom Squadron, comic-panel dialog, four-weapon arsenal.
- **Phase 4** — Cynthia unlock for endless mode, PWA manifest + service worker, polish.

All four phases are implemented in `index.html`.

## File layout

```
/index.html          single-file game (HTML/CSS/canvas JS)
/manifest.json       PWA manifest
/service-worker.js   offline shell cache
/assets/audio/       Starshield_Loop.mp3 (drop other tracks here as filenames)
/assets/img/         starfield, character portraits, enemy dossier
```

## Audio drop-in

The audio manager loads tracks by filename relative to `assets/audio/` with graceful fallback:

- `Starshield_Loop.mp3` — gameplay loop (included)
- `Boss_Theme.mp3` — boss waves (optional)
- `Title_Theme.mp3` — title screen (optional)
- `Wave_Clear.mp3` — wave-clear sting (optional)
- `Game_Over.mp3` — game over (optional)

Missing files log a console warning and do not break gameplay. Sound effects (shoot/hit/explode/yeet/etc.) are synthesized at runtime via WebAudio — no extra files needed.

## Enemies

| # | Name | Behavior |
|---|------|----------|
| 1 | Steroid Canister | Explodes on impact, releases pink plasma bullets in 6 directions |
| 2 | Space Octopus | Close range, high speed, inky homing bullets |
| 3 | Comet Leech | Homes in on player, leaves fire trail |
| 4 | Satellite Swarmer | Orbital movement, calls reinforcement steroids |
| 5 | Black Friday Bot | Armored (requires high-damage weapon), drops items, debris explosion |
| 6 | Abductor UFO | Heavy green shield, tractor-beam fires triple plasma |
| 7 | Tentaclon Elite | Boss — ink walls, summons octopus minions at HP thresholds |
| 8 | Grav-Turret | Stationary boss — heavy spread fire |
| 9 | Steroid Mothership | XL boss — multiple weakpoints, armor cracks in phase 2 |
| 10 | The Moon | XXL final boss — moon rocks alternating with tracking laser eyes, has mood swings |

## Bosses

- Boss waves replace regular spawning. Boss has its own HP bar (top of screen).
- At each phase threshold (75/50/25%, varies by boss) a burst of small octopuses spawns as backup. Those minions still hit Earth if they get through.
- Boss death triggers `WAVE CLEAR` and resumes regular spawning into the next wave.

## Octopus family

- Standard small octopus — recurring mid-wave enemy from wave 3 onward
- **Squirt** (wave 5) — first octopus boss
- **Tentaclon Elite** — mid-campaign octopus boss
- **The Kraken** — endless-mode escalation boss

Each phases attacks at HP thresholds.

## Doom Squadron

Fill the magenta meter (bottom of Starshield card) through kills. Tap the bar when full to deploy Hank & Tank for 30 seconds:
- Triple fire rate, side cover fire from the screen edges, magenta bullet tracers.
- Hank + Tank portrait bubbles appear over the sides during deployment.

## Planetary Yeet

Tap the green button (or press `Y`). 22-second cooldown shown as a conic sweep around the button. Sends a giant neon planet flying across the screen, clears all enemies and hostile bullets, deals ~12% max-HP damage to a boss.

## Weapons

- **PEW CANNON** — balanced default (lv1 single, lv2 triple, lv3 5-shot fan)
- **LASER LANCE** — focused, high-damage piercing bolt (lv2/3 add lateral beams)
- **SHREDDER MISSILES** — multi-target lock-on homing (lv2/3 increase salvo)
- **EARTHQUAKE BOMB** — slow, big AOE bolt + screen shake

Weapon power-ups (magenta `W` orb) upgrade level, then cycle to the next weapon.

## Comic dialog system

Triggered: campaign start, certain wave starts, every boss incoming, victory, game over. Tap twice on a panel to advance. Cynthia is "captured" the entire campaign. She is **handling it.** The end reveal explains everything.

## Cynthia unlockable

Beat the campaign (clear The Moon) to unlock Cynthia in endless mode. Different playstyle: faster ship, fewer lives, and her infiltration mechanic occasionally flips enemies to fire at each other.

## TWA / Ads notes

The architecture is ready for TWA wrapping. AdMob ad and `$0.99` remove-ads IAP placement is *not* implemented in this build — the game runs entirely free and offline. When wrapping:

- Inject AdMob initialization into the TWA shell, not the web build.
- Gate IAP unlock via a `localStorage` flag (e.g. `ed_noads='1'`) so the same HTML can opt out of ad surfaces if/when added.
