# FLOAT KNIGHTS

🌐 Live: <https://perchance.org/float-knights>

A bracketed tournament sim where cute animal mascots in medieval armor fight
each other with futuristic weapons. Each knight has three balloons strapped to
their back (Mario Kart 64 style). Pop all three and they're knocked out.
Knock them out three times and they're dead — *permanently* — and their gear
drops on the field for survivors to scavenge.

## What's in v7 (the "play-itself" rewrite)

### Auto-play + Toast notifications

The game now plays itself. Between every round, match, and season there's a
**60-second cooldown** during which you can place bets, shop, switch tabs,
or just watch the carnage settle. When the timer hits zero (or you click the
"skip ▸" link in the arena header), the next event begins automatically.

Bet outcomes and major events fire as **toast notifications** in the
bottom-right corner:

- 🟢 **Green** — bets won, payouts received
- 🔴 **Red** — bets lost
- 🟡 **Yellow** — warnings (autoplay paused, refunds, etc.)
- 🔵 **Blue** — info (round/match/season summaries, casualty announcements)

There's an **AUTOPLAY toggle** in the arena controls to pause auto-advancement
indefinitely if you want to manually advance each round.

### 16:9 arena

The arena is now a proper **960×540 widescreen** rectangle instead of a
600×600 square. Spawn radius widened, scenery repositioned, more breathing
room for ranged combat.

### Projectile physics

Ranged weapons (Laser Pistol, Railgun Rifle) now fire **dodgeable, fire-and-
forget projectiles** instead of resolving instantly. Targets can sidestep
shots, and accuracy degrades with both distance to target and target
velocity. Melee weapons (Riot Shield, Stun Baton, Plasma Mace) still resolve
on contact.

| Weapon         | Range | Projectile speed | Baseline accuracy |
|----------------|------:|-----------------:|------------------:|
| Riot Shield    | 22    | (melee)          | (melee)           |
| Stun Baton     | 22    | (melee)          | (melee)           |
| Plasma Mace    | 26    | (melee)          | (melee)           |
| Laser Pistol   | 230   | 14 px/frame      | ±0.05 rad         |
| Railgun Rifle  | 460   | 22 px/frame      | ±0.025 rad        |

### Terrain obstacles

Each match generates **3-7 stone blocks** (36×36px each) clustered near the
center vertical line, randomly nudged left or right so no two matches feel
the same. Players path around them. Projectiles shatter on impact.

### Smarter AI

A real state machine instead of "walk at enemy, attack":

- **ENGAGE** — pursue target, attack when in range. Ranged knights kite to
  maintain a comfortable ~220px firing distance; melee knights close hard.
- **SEEK_ITEM** — when no immediate threat is nearby, walk toward a
  desirable gear upgrade visible within 200px.
- **FLEE** — at HP < 25%, run away from the nearest threat. Ranged knights
  fire desperate parting shots while retreating.
- **FINISH_OFF** — if someone has shot us recently, prioritize engaging
  *them* over chasing fleeing low-HP targets (a "mercy" mechanic — let the
  weakling go, kill the one trying to kill you).

Each AI's state shows as a tiny emoji badge above their head: 🏃 fleeing,
🎒 seeking item, 🎯 finishing off an attacker.

### Player spacing fix

Knights now have a soft **separation force** that pushes them apart at <22px
distance. No more clipping into each other — they form proper formations and
flow around obstacles.

### New mascot avatars

Each knight is now rendered as a **🤖 Robot emoji** with their team's mascot
emoji (🦊 🦫 🦉 🦈) stacked on top like a costume mascot helmet, with a
team-colored halo behind them. The balloons-as-HP trail above the helmet,
and the weapon icon shows next to the body.

### Pre-match items spawn

6 random items spawn on the field at the start of round 1 (and 2 more on
later rounds if the field is sparse). This gives the AI reasons to scout
the map instead of just rushing each other immediately.

### Position-aware gear scoring

Snipers prefer Railguns. Tanks prefer Shields. Speedsters prefer Lasers.
Etc. Scavenging is no longer "always swap to the strongest" — knights
weight gear by archetype fit, so loadouts stay diverse.

### Legendary item foundation

Every gear piece now tracks a `scavengedCount`. Once a piece has been
scavenged 3+ times it's marked **⭐LEGENDARY⭐** in the commentary feed and
renders with a magenta glow on the field. Full lineage tracking (history of
who carried it, where it was lost) is on the roadmap.

## Tabbed UI (Game · Bet · Store · About)

Top-level tab nav swaps the entire main content area:

- **📺 Game** — 3-column broadcast: rosters, arena, bracket, commentary
- **🎲 Bet** — three markets (Match Winner / Next Round Winner / Last Standing)
  + active bet history table
- **🏪 Store** — item grid + Sandbag "COMING SOON" placeholder
- **ℹ️ About** — credits, roadmap, inspirations

The right rail shows a read-only "Your Bets" summary with a "Place a bet →"
link to the Bet tab. Site footer with WestNinja's links is visible on every tab.

## Tournament structure

- 4 teams × 5 knights (Tank / Striker / Speedster / Brawler / Sniper)
- Single-elim bracket per season: 4 → 2 semifinals → 1 final
- **Best-of-5 rounds per match** (first to 3 round wins)
- **3 lives per knight per match** — KO costs one life, respawns next round;
  3 KOs = permanent death + gear drops
- Random reseeding every season
- Cloud sync via `uploadPlugin` — pick a username, click Save

## Roadmap

- **Voluntary AI gear-drops** — high-scavenged items get deliberately dropped
  by their owner before risky engagements to maximize the chance they're
  picked up by an ally and live another round
- **Lost-item history** — track items that didn't get picked up by end of
  match (lost to the void)
- **Sandbag store item** — high-cost, attach to a specific knight to slow
  them down. Subtle match-weighting for high cost
- **Public chatrooms** (global / per-team) — alongside the bet tab
- **Knight memorial pages** — full career obituary including which items
  they carried into the grave

## Setup on Perchance

1. Visit <https://perchance.org/float-knights> in edit mode (or create a new
   generator at <https://perchance.org/welcome>).
2. Paste `perchance-top.txt` into the **TOP editor**.
3. Paste `index.html` into the **HTML panel**.
4. Save and view.

## Tunables

```js
const MATCH = {
  bestOf: 5, roundsToWin: 3, startingLives: 3,
};
const COMBAT = {
  arenaW: 960, arenaH: 540,
  hitRange: 18,           // melee strike distance
  roundMaxFrames: 800,    // safety cap per round
  // ...
};
const AUTOPLAY = {
  enabled: true,
  cooldownRoundMs:  60000,  // 1 min between rounds
  cooldownMatchMs:  60000,
  cooldownSeasonMs: 60000,
};
// In _part2_sim_a.html, weapon range/projSpeed/inacc tune ranged combat;
// stat modifiers (defMod, dmgMod, cdMod, critMod) are for both melee & ranged.
```

## Smoke-test verified

A 200-frame simulation:

- 3 terrain blocks generated (e.g. at x=504, x=395, x=542 — clustered near
  center, randomly nudged ±90px)
- **Zero player-inside-terrain incidents** across 200 frames (collision
  works)
- **Min player separation: 12.7px** (separation force keeps them from
  stacking on top of each other)

A representative 8-season run:

- Champions varied across runs (3-4 different teams winning per 8-season
  block)
- Avg casualties: ~13 per season (down from v6's ~17 — terrain + dodging +
  flee state lets weaker knights survive)
- A representative match: best-of-5 went to **3-2** in 1521 ticks across all
  5 rounds (vs the v6 era's frequent 3-0 sweeps)
- Snipers thrive in projectile combat: top career stat in one run was a
  Sniper at 50 kills (15+ matches lifetime)

## Credits

Built by [therealwestninja](https://github.com/therealwestninja) ·
[DeviantArt](https://www.deviantart.com/west-ninja).

Random first-name generator by @WestNinja (Feb 2020), adapted from a
Shakespearean insult generator by Darren G. Holloway, Jerry Maguire, and
J. Kessels.

Inspirations: *Blaseball* (permanent mortality + emergent narrative),
*Mario Kart 64 Battle Mode* (balloon HP), *Worms* (gear pickup chaos),
*Salem / UO* (item naming after dead players).

## v8 patch — terrain integrity, health pickups, smarter pathing

- **Projectiles now properly stop on terrain** — swept collision (Liang-Barsky
  segment vs AABB) over each projectile's per-tick path. Verified 0
  projectile-frames spent inside terrain across 1,134 sampled shots.
- **Line-of-sight check on attacks** — ranged AI no longer fires at targets
  behind terrain. They close the distance instead.
- **Corner-stuck fix** — when wall-slide returns zero motion, AI strafes
  perpendicular for ~18 frames to round the corner.
- **Health Pack pickups** — new ❤ red-cross drop type. 3 spawned at every
  match start (plus 2 guaranteed Armor pieces and 3 random gear). Health
  Packs are consumed on contact (+30 HP, capped at max).
- **FLEE seeks health** — at HP <25%, AI now runs toward the nearest Health
  Pack within 320px instead of just running away. Falls back to fleeing-
  away if no Pack is in range.
- **Item-on-the-path heuristic** — when calculating which drop is worth a
  detour, AI now weighs alignment with the path to its current target.
  Drops directly between the AI and its target get a +6 utility bonus over
  drops behind it.

## v8.1 patch — proper Perchance generator structure

### Setting up the generator

1. Go to <https://perchance.org/welcome> and create a new generator (or
   open the existing one at <https://perchance.org/float-knights> in edit
   mode).
2. **TOP editor** — paste the contents of `perchance-top.txt`. This declares:
   - `uploadPlugin = {import:upload-plugin}` — needed for cloud sync save/load
   - `superFetch = {import:super-fetch}` — needed for CORS-bypassing the
     uploads.perchance.org domain when loading saves
   - `output {html}` — substitutes the entire HTML panel as the page output
3. **HTML panel** — paste the full contents of `index.html`. This is the
   game itself — markup, CSS, and JS in one bundle.
4. Click Save, then "view generator". The game should boot and start a
   match within a few seconds.

### About the square-bracket gotcha

Perchance's template engine treats `[js]` and `{listName}` as substitution
sites — anything between brackets gets evaluated and replaced. Inside the
HTML panel the substitution generally only runs once (during the `{html}`
output substitution), and content inside `<script>...</script>` blocks is
left alone in practice.

**However**, any literal `[Word]` you write in plain HTML *body text*
(outside `<script>` tags) WILL get interpreted as a JS expression by
Perchance and replaced with whatever evaluating that identifier returns
(usually `undefined`, which silently deletes your text).

Three places in v8 had this bug — the About-tab description of the
scavenged-gear naming convention used literal `[Owner]'s [Gear]`, plus two
matching code comments. All three have been fixed:

- HTML body text now uses HTML entities: `&#91;Owner&#93;'s &#91;Gear&#93;`
  which renders as `[Owner]'s [Gear]` in the browser without Perchance
  touching it.
- Code comments were reworded to use angle-brackets `<owner>'s <gear>`
  which Perchance ignores (HTML-style, but inside JS comments so the
  browser also ignores them).

If you add new HTML content with literal square brackets in body text,
remember to use `&#91;` and `&#93;` for `[` and `]`. Same trick for
literal curly braces: `&#123;` for `{` and `&#125;` for `}`.
