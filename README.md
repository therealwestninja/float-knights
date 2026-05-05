# FLOAT KNIGHTS

![Logo](/title-card-small.jpg)

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

## v9 patch — automation, draws, 12 teams, Settings tab

### Bug fixes
- **Persistence** — game state now auto-saves to localStorage on every
  state change (round/match/season end, bet placed, item bought, settings
  applied). Restored automatically on page reload. Cloud sync via
  uploadPlugin still works for cross-device.
- **Roster repopulation** — `reapDeceased()` now also runs at the start
  of every match (not just between matches), so a team can never enter a
  match with fewer than 5 living knights. Fixes the "season ends early
  with empty teams" bug.
- **Skip buttons removed** — game is fully autoplay; the "Skip Round"
  button and the countdown-pill skip-link are gone. Use AUTOPLAY toggle
  to pause indefinitely, or 4× speed to fast-forward.
- **Centered countdown overlay** — instead of a tiny pill in the arena
  header, the round/match/season cooldown is now a giant 96px timer
  centered over the arena, with the canvas blurred and dimmed behind it.
  Pulses red at <5 seconds.

### Features
- **Draw mechanic** — both round-level (timeout with both teams alive +
  equal kill counts → draw, no point awarded) and match-level (best-of-N
  rounds played, score still tied → match draw, coin flip determines
  bracket advancement, all match bets refunded). Records track
  wins/losses/draws.
- **Settings tab** — 6 user-configurable tunables:
  - Time between rounds (10–300s, default 60s)
  - Time between matches (10–600s, default 60s)
  - Time between seasons (10–600s, default 60s)
  - Rounds per match (1–9, odd only, default 5)
  - Lives per knight (1–9, default 3)
  - Bracket format: 4 / 8 / 12 teams
- **12-team league** — full TEAMS array now contains 12 mascot teams
  (Foxes, Beavers, Owls, Sharks, Pandas, Raccoons, Frogs, Hedgehogs,
  Kits, Wolves, Badgers, Otters). The 12-team bracket format gives top
  4 ranked teams byes; bottom 8 play 4 qualifier matches; 4 winners join
  the top 4 in an 8-team ladder (qf → sf → final). 11 matches per season.
- **Generalized bracket logic** — `buildBracket()`/`advanceBracket()`
  now take a `LEAGUE_CONFIG.bracketSize` config (4/8/12) and walk
  through bracket slots via an `_order` array. Bracket strip in the UI
  rebuilds dynamically based on bracket size.

### Verified
- Smoke test passes 8 seasons across the 12-team roster, all teams
  end with full 5-player rosters
- 5 different teams won across an 8-season sample (vs the 4-team era's
  typical 2-3)
- Bracket format switching works for all three sizes (3, 7, 11 slots)
- Serialize/deserialize roundtrip preserves new fields (LEAGUE_CONFIG,
  AUTOPLAY cooldowns, MATCH bestOf/lives)
- Draws are recordable in the rounds[] log via `isDraw: true` flag

### See also
- `ROADMAP.md` — full retrospective of unsurfaced ideas plus the
  architectural plan for **persistent shared live matches**
  (deterministic seeded sim + wall-clock-locked schedule + uploadPlugin
  checkpoints)

## v10 patch — deterministic simulation foundation

This is **Phase 1 of the persistent shared live match** roadmap entry. The
sim is now fully deterministic: same seed → byte-identical match outcomes.

### What changed
- All randomness in the simulation flows through a seeded **mulberry32 PRNG**
  (`SIM_RNG`). Real `Math.random()` is preserved only for the one-time
  generation of a fresh league's genesis seed.
- Every match gets a deterministic seed derived from
  `hash(genesisSeed, season, slot)`. The same league running the same
  bracket position always produces the same match.
- `initLeague({genesisSeed})` accepts a seed for reproducibility. Saved
  leagues persist their `genesisSeed`, so reloading replays identically.
- New **match log** in History modal: each completed match is recorded with
  its `(season, slot, seed, teams, score, winner, casualties)` tuple. The
  League's genesis seed shows at the bottom.

### Verified
A determinism test ran the same `genesisSeed: 0x12345678` twice and
compared outcomes:
- **Run 1**: `otr` (Otters) vs `pnd` (Pandas) → `pnd` wins 1-3 in 940 ticks,
  6 casualties (Ghrvrio Quillbeard II, Clusva Marshmallow III, etc.)
- **Run 2**: identical — same teams, same score, same casualties in the
  same order.
- A different seed (`0x99999999`) produced a different matchup
  (`fox` vs `otr` → `otr` wins 1-3, different casualty list).

### What this unlocks
The sim is now the foundation for the **shared live match** goal. Phases
2 and 3 are still ahead:
- **Phase 2**: Wall-clock-locked schedule (any client opening the page
  knows what match should be live right now from `Date.now() - GENESIS`).
- **Phase 3**: uploadPlugin checkpoint pull/push (new visitors don't have
  to replay from genesis; they fetch the latest checkpoint and replay
  forward to the current frame).

See `ROADMAP.md` Tier 1 for the architecture writeup.

## v11 patch — emergent storytelling

Builds on v10's deterministic foundation with four features that make the
league's history *legible*: knights become characters, items become
legendary, the Bet tab gets strategically deeper.

### Knight memorial pages
The Knight Profile modal is now a full memorial: K/D ratio, longest
kill-streak, current streak (with a "🔥 ON A 5-KILL STREAK" callout if
≥3), recent victims (with the season + round they fell in). Deceased
knights show "⚰ Knight Memorial" as the title. The Graveyard list is now
clickable — tap any name to read their memorial.

### Kill streaks
Each knight tracks `currentStreak` and `longestStreak`. Streaks reset on
KO (whether they keep their life or not — getting knocked down ends your
streak). Milestone streaks (5, 10, 15, 25) fire toasts and feed entries:
"🔥 X is on a 5-kill streak!" When someone ENDS a long streak (5+ kills),
that fires a separate "💔 X ENDS Y's 8-kill streak" callout. Verified: a
sample match produced 6 individual streaks reaching as high as 6 kills,
with 23 total deaths recorded.

### Lost-item history
At match end, any gear that's been scavenged 2+ times AND wasn't picked
up by anyone is now permanently LOST (logged in `LEAGUE.lostItems`). A
new "📉 LOST LEGENDARY ITEMS" section appears in the Graveyard modal,
showing the season, item name, scavenge count, and last carrier. Each
lost item also fires a yellow toast at the moment of loss. Sample run:
14 legendary items lost in a single match — they had stories.

### Live odds re-computation
The Bet tab's odds now use **live match state** (current lives, current
HP, gear-modified stats) for participating teams while a match is in
progress. Outside a match (or for non-participating teams), it falls
back to roster baseline. Verified: a match that started at `1.89 / 1.91`
(near-balanced) shifted to `1.83 / 1.98` after round 1 as one team took
casualties. Round bets are now strategically meaningful — buying into a
team that's down 0-2 with 2 dead knights gets significantly longer odds
than at match start.

### Aggregate state changes
- `career.deaths`, `career.longestStreak`, `career.currentStreak` added
  per-knight (auto-backfilled to 0 on save load)
- `LEAGUE.lostItems[]` array — persisted across saves
- Match-end gear scan checks `scavengedCount >= 2` for "lost legendary"
- `teamStrength()` is now match-aware via `LEAGUE.match.phase !== "post"`

## v12 patch — wall-clock catch-up (Phase 2 partial)

The league now runs in real time even when the tab is closed. Come back
after 20 minutes, the sim has played through whatever matches would have
happened. Combined with v10's deterministic seeding, this means the
matches played in your absence happen *exactly* as they would have if
you'd watched them live.

### How it works
- `LEAGUE.startedAt` set once on `initLeague()` (immutable for the league's lifetime)
- `LEAGUE.lastTickAt` updated every animation frame to `Date.now()`
- Both fields persist in the localStorage save
- On boot, if `Date.now() - lastTickAt > 5s`, call `fastForwardSim(elapsed)`
  which loops through ticks and cooldowns until the elapsed time is consumed

### Catch-up summary banner
On resume, the user sees a feed entry + toast:

> ⏩ Caught up ~24 min — 1 season, 3 matches happened while you were away.

If catching up exceeds the 1-hour cap (e.g. tab was closed for a week),
a separate warning toast fires:

> Catch-up capped at 1 hour. Some events not replayed.

### Limits
- **1 hour cap** on wall-clock catch-up. Closing the tab for 8 hours
  catches up the most recent 1 hour and resumes from there.
- **5 second wall-clock budget** on the fast-forward computation. Slow
  devices won't freeze on resume; if the budget hits, the sim resumes
  partial-way through and natural ticks take over.
- **Per-league wall-clock** — each user's league has its OWN
  `startedAt`. Two users with different `genesisSeed`s see different
  leagues that progress at the same wall-clock rate. True canonical
  scheduling (everyone watching the SAME league) is Phase 3 and
  requires uploadPlugin checkpointing.

### Verified
- 30 min fast-forward → 6 matches across 1.5 seasons
- Determinism preserved through fast-forward (same starting state +
  same elapsed time = identical match history)
- 5-hour fast-forward correctly capped at 1 hour, producing 11 matches
  across 3 seasons (vs the un-capped 30+ matches that would otherwise run)
- Draw-rendering bug found and fixed (round-strip pips for drawn
  rounds now show as muted gray instead of crashing on the lookup)

## v13–v14 patch — TV broadcast UI + League Details tab

### v13 — Broadcast aesthetic + AI fixes
- **Stock-ticker bar** at bottom of viewport (fixed 36px, gold-accent border).
  Three sections: live scores left (current match + last 3 of season), rolling
  marquee feed center (champion banner, top streaks, lost legendaries, recent
  feed entries), gold "⚔ FLOAT KNIGHTS" brand right.
- **Arena floor visual upgrade**: vertical sky→warm gradient, team-colored
  end zones, bold center vertical line, double-ring center circle, faded
  "FK" brand mark in center, vignette around edges.
- **Bigger arena layout**: max-width 1280→1600px, side rails slimmed.
- **Hamburger dropdown nav** at <900px viewport — tabs collapse into a
  slide-down sheet from the header. Auto-closes on selection.
- **Power Core objective** — golden pulsing core spawns at arena center
  10sec into each round. Walking into it grants the capturing team a
  +25% damage buff for 8 seconds. Respawns 30s after capture.
- **New AI state SEEK_CORE** — when the core is active and the team
  doesn't have the buff, AI within 280px abandons combat to grab it.
  Snipers especially incentivized (their range advantage means they can
  cover the core, but they have to come to it).
- **Sniper corner-camping fix**: snipers now prefer 50% of weapon range
  (200px) instead of 60% (276px). When kiting near walls, blend back-step
  angle 40-80% toward arena center. **Verified: 0% of 468 sampled sniper
  frames spent within 80px of any wall** (vs the prior known bug).

### v14 — League Details tab
A new dedicated tab covering the league's fiction, performance breakdown,
and emergent histories.

#### Hero block
League stats at a glance: current season, total matches played, total
casualties, total lost legendary items.

#### Teams (12 cards, ranked)
Each team gets:
- Team-colored top border + emoji avatar
- Founded year + short code
- Italic motto (e.g. SHK: *"Smell blood. Move first."*)
- Multi-sentence backstory (every team got real lore — 200+ chars each:
  the Beavers hand-forge their own weapons, the Owls were nocturnal pit-
  fight champions before going legitimate, the Raccoons have the highest
  scavenge ratio in the league, etc.)
- W/L/D record + championship trophies

#### Stats Breakdown table
10-column matrix: Team / W / L / D / 🏆 / Kills / Deaths / Scavenged /
Longest Streak / Active roster size. League-best values (kills, scavenged,
streak, championships) highlighted gold.

#### Hall of Fallen Heroes
Every dead knight from `LEAGUE.graveyard`, sorted by career kills (so the
most legendary fallen show first). Each card shows team, season+round of
death, position, killer, total career kills + longest streak. Click any
card to open the full Knight Memorial modal.

#### Biggest Loot Losses
The `LEAGUE.lostItems[]` array sorted by scavenge count descending. Each
loss shown as a card with: 32px item icon, item display name (which is
typically the lineage like "Wylmnell Bubblesnap the Bold's Plate Cuirass
(scavenged)"), where it was lost (season + slot), last carrier name, and
a big purple "Nx SCAVENGED" stamp on the right. The bigger the chain, the
more legendary the heartbreak.

### Verified
- All 12 teams have lore + motto + founded year
- Power Core spawns at frame 200, captured ~27 frames later by AI
- Snipers stay out of corners (0/468 wall-frames sampled)
- League tab populates from same data sources as the rest of the app
- After 3 matches: 14 graveyard entries, 1 lost legendary item, 3 match
  records — League tab renders all sections correctly with this data

## v15 patch — combat lethality rebalance + armor wiring

The single biggest tuning pass since v6. Two interlocking problems
addressed: rounds were ~15s instead of the intended ~45s, and the AI
mostly ignored armor pickups while armor barely affected damage anyway.

### Combat lethality rebalance
- **HP**: rolled range 70–120 → 140–200, cap 150 → 240 (~2× durability)
- **DEF**: rolled range 5–25 → 8–30, cap 50 → 70 (slightly higher floor and ceiling)
- **Armor formula**: `def / (def + 35)` instead of `def / (def + 50)` —
  every point of defense reduces damage ~40% more meaningfully.
  A Tank with 50 total defense now takes 41% damage (was 50%).
- **Crit multiplier**: 2.0 → 1.7 (crits less swingy)
- **Cooldown multiplier**: ×1.20 globally (`COMBAT.cdMult`) — ~20% slower attacks
- **Health Pack heal**: 30 → 60 (scales with the bigger HP pool)
- **Round safety cap**: 800 → 1500 frames (rounds can now legitimately
  reach 75s before the timeout coin-flip kicks in)

**Verified**: 6-match sample produced average round duration of **44.9 seconds**
(target was ~45s). Min 17s, max 75s, natural variance.

### AI armor priorities
- **`gearScore` for armor weights** doubled — `defMod * 2.5` (was 1.5),
  `spdMod * 8` (was 6). Position-specific bonuses added: Tanks get +4
  for heavy armor, Speedsters +4 for spd-mod armor, Snipers +3 for
  crit-mod armor.
- **`findBestDropFor`** detection radius widened 220 → 340px (covers
  most of one half of the arena), distance penalty reduced 0.025 →
  0.018, and armor upgrades get a +4 utility floor.
- **`SEEK_ITEM` threshold dropped 90 → 55px** for the "no nearby threat"
  gate — AI now disengages briefly to grab pickups during light pressure.
- **Urgent pickups bypass the gate**: low HP + Health Pack within range,
  OR low armor + Armor pickup within range, both fire SEEK_ITEM
  regardless of nearby enemies.
- **6-match sample**: 18 scavenge events across all knights (well
  above the ~5–7 the old gating produced).

### New armor effects
- **Stun resistance** — every point of total defense reduces stun
  probability by 1.5% (capped at 85%). A heavy-plate Tank with def=50
  reduces a Stun Baton's 50% stun chance to **13% effective**.
  Resisted stuns show a 🛡 float over the defender.
- **Knockback** — every hit pushes the defender back proportional to
  damage, but armor cuts the push (15% reduction per def point, capped
  at 80%). Light-armor Speedsters visibly bounce on hits;
  Tanks barely flinch. Knockback respects terrain (no clipping).

### More pickups
- **Match-start spawns** raised: 4 Health Packs (was 3) + 3 Armor
  pieces (was 2) + 3 random gear = 10 total drops at round 1.
- **Periodic mid-round Health Pack resupply** every 25 sec of round
  time, capped at 3 health packs on the field (so the field doesn't
  saturate). Long matches stay sustainable.

### Verified end-to-end
- Average round = 44.9s (target met)
- 18 scavenge events across 6-match sample (vs ~5–7 before)
- Determinism still holds — same seed produces identical match results
- Power Core still spawns at frame 200 (10s in)
- Stun resist working as designed (50% raw → 13% effective vs def=50)

## v16 patch — League legends, store rarity tiers, expanded betting

### Seeded "League Legends" — content for brand-new users
The Hall of Fallen Heroes and Biggest Loot Losses were empty for users
who had just opened the page. v16 ships a baked-in canon: 10 named
founding heroes and 7 legendary lost items with full lineage chains.
These are visible in the **Settings tab** (new "LEAGUE LEGENDS" +
"LEGENDARY LOST LOOT" sections) and merge with the user's own
graveyard/lostItems on the **League tab** (current generation always
sorts above legacy entries).

Sample heroes baked in:
- **Old Bristlebeard the First** (BVR tank, S1 R5) — first Beaver to die
  in the league, 47 career kills, fell defending the inaugural final
- **Vela Hootington** (OWL speed, S3 R7) — held the league streak record
  at 22 before being ambushed at the Power Core
- **Crash Wartooth** (WLF brawl, S6 R3) — stunned three times in twelve
  seconds; the Wolves now insist on heavy plate for all Brawlers per
  "the Crash Rule"

Sample legendary loot:
- **Bristlebeard's Riot Shield (scavenged)** — the most-scavenged item
  in league history, carried by 7 knights across 6 seasons before
  slipping into the void
- **Vela's Plate Cuirass (scavenged)** — survived its first owner only
  because she let her teammate borrow it for a single round
- **Brask's Brigandine (scavenged)** — the only armor in league history
  known to have switched teams (Foxes → Pandas → Wolves)

Legacy entries are tagged with a purple/gold "★ LEGEND" badge so
players can distinguish canon from their own emergent stories.

### Store overhaul — rarity tiers + Sandbag shipped
Previously 6 flat-priced items. Now 12 items grouped into three
visible rarity tiers with distinct borders (gold for legendary,
purple for rare, default for common):

**Common (6):** Iron Plating (+10 DEF), Sharpener (+5 DMG), Track
Spikes (+0.6 SPD), Healing Tonic (+30 HP), Lucky Charm (+5% crit),
Saboteur (opp -3 DMG)

**Rare (4):** War Paint (+8 DMG +5 DEF), Combat Stim (+0.4 SPD +5
DMG), **Sandbag** (opp -0.5 SPD — the long-roadmapped knight-debuff
finally landed), Smokescreen (opp -2 DMG -3% crit)

**Legendary (2):** Elixir of Endurance (+50 HP +10 DEF), Curse of
the Loser (opp -10 HP -5 DEF -3 DMG)

Each card now also shows a "buff your team" / "debuff opponent"
tag in green/red so the targeting is obvious before you spend bits.
The "COMING SOON" sandbag placeholder card has been removed.

### Three new betting markets
The bet tab previously had three markets (Match Winner, Round
Winner, Last Standing). v16 adds three more:

- **First Kill** — pick which team draws first blood. Pays match
  odds × 0.95. **Locks server-side** the moment the first kill is
  scored (UI hides the section, backend rejects late bets).
- **Total Casualties** — over/under 5 deaths in the match.
  Flat 1.85× payout. Resolves at match end.
- **Match Length** — over 3 / under 4 rounds played.
  2.10× over / 1.65× under. Most matches are 3-round sweeps so
  "over" is the longshot.

Each market has its own state badge (OPEN / LOCKED / CLOSED) and
the bet selection label updates to show the chosen market clearly.

### Bugs caught and fixed during the debug pass
- **`showIntermission` crashed on draw outcomes** — when a match
  ended in a true draw (equal lives, equal score), `winnerId` was
  null and the toast tried to read `winner.emoji`. Now branches
  to a "match ends in DRAW" toast and shows the coin-flipped
  advancing team in the feed.
- **8/12-team bracket labels showed "Final" for everything that
  wasn't a semifinal** — the slot label switch only knew about
  semi1/semi2/final. Now uses a comprehensive map covering
  qualifiers, quarterfinals, semifinals, and the final.
- **`placeBet("firstkill", ...)` was accepted after the kill was
  already captured** — UI hid the section but the backend would
  still take the bet (always losing for the user since the
  market was already resolved). Now rejected server-side once
  `m.firstKillTeam` is set.

### Verified end-to-end
- Brand-new league shows 10 legacy heroes + 7 legacy loot from
  boot, before any matches play
- Sandbag purchased for 80 bits applies -0.5 SPD to the opponent's
  pendingBoosts (visible in the next match)
- 8 consecutive matches across multiple seasons: 0 crashes,
  including any that ended in draws
- Determinism preserved: same seed (`0x55555`) produces identical
  match result before and after all v16 changes (1-3 pnd)

## v17 patch — death cries + power-core fixes + bug squash

### Worms-style death cries (NEW)
Every KO now triggers a one-line last-words quip from the falling
knight. Cries are pulled from a tonally-categorized catalog of ~50+
lines, with the pool selected by context:

- **Self-destruct** ("I held the grenade too long.", "Whoops.")
  — fires when a knight kills themselves with their own item
- **Permadeath** ("Goodbye, cruel arena…", "Tell the children I tried.")
  — fires when the knight has used all 3 lives
- **Streak broken** ("ALL THAT WORK!", "I was COOKING!")
  — fires when the knight had a 3+ kill streak going
- **Stunned** ("Z…z…z…", "Cheap shot!")
  — fires when KO'd while stunned
- **Crit'd** ("OW.", "MY BALLOONS!", "Read the room!")
  — fires when the killing blow was a critical hit
- **Generic** ("Tell my mother…", "Worth it.", "I had a family of
  imaginary squirrels.") — the default catch-all pool

Each cry shows as a quoted float over the corpse for ~2.75 sec, and
gets pushed to the feed where it rolls through the bottom ticker.
Permadeath cries float in legendary-purple; everyone else in white.
Verified: 17 kills produced 14 unique cries — the catalog is large
enough that repetition fatigue is minimal across normal play.

The architecture leaves room for ai-text-plugin integration later
— the `deathCryFor()` function is the hook point. For now the
static catalog ships immediately and works offline; AI-generated
cries can layer on top without restructuring the call site.

### Power Core fixes (TWO bugs)

**Bug 1: Power Core could spawn inside a randomly-placed terrain
block.** The terrain generator clusters obstacles along the center
line — exactly where the Power Core sits. If a block landed within
~50px of the arena center, the Core was unreachable. Fixed in
`generateTerrain()`: a 70px keep-out radius around the center is
enforced. Any block that would land inside the keep-out gets pushed
outward along its current vector from center until it clears, then
clamped to arena bounds. **Verified: 50/50 trials with random seeds
produced zero overlaps.**

**Bug 2: AI prioritized the Power Core too aggressively** — every
knight within 280px would abandon their fight to chase it, gutting
combat engagement. Three tightening changes:

1. **Only the team's closest knight** to the Core seeks it. Everyone
   else stays focused on combat.
2. **Search radius dropped 280 → 180px.** Knights have to be
   genuinely nearby; cross-arena pursuits don't fire.
3. **Enemy-distance gate raised to 110px.** A knight under direct
   threat doesn't disengage.
4. **Post-capture cooldown** — for 6 seconds after capturing,
   teammates won't re-seek the same Core (the buff is active anyway).

**Verified: SEEK_CORE went from "the dominant AI state when Core
active" to 5.3% of total AI decisions** — combined combat states
(ENGAGE 47.2% + FINISH_OFF 35.0%) now occupy 82.2% of bot behavior.
The Core is still a meaningful objective, but it's a tactical
detour, not the entire strategy.

### Bug 3: Removed the broken HTML-entity-escaped scavenged-name
example from the About page. The line used `&#91;` and `&#93;`
brackets to display literal `[Owner]` and `[Gear]` placeholders,
but bracket characters in Perchance generator HTML are interpreted
even when entity-encoded. The whole line is removed (per user
request — "do not fix this line, delete this line"). The scavenged
naming behavior is still documented elsewhere in the README and
in the in-game gear chips.

### Verified end-to-end
- Determinism preserved across all changes — same seed (`0xABABAB`)
  produces identical 1-3 frg result before and after
- Power Core spawn-overlap: 0/50 random trials
- AI state distribution: only 5.3% SEEK_CORE during Core-active
  frames (was effectively 100% before)
- Death cries: 14 unique quips from 17 kills, mix of all six
  tonal categories

## v18 patch — dodging + Healer + Grenadier + cloud-sync fix

This is the biggest single update since v15. Roster size grows from
5 to 7 with two new specialist positions, projectile-dodging AI ships
in earnest with proper position-specific tuning, the cloud-sync error
is fixed by reading the actual Perchance API spec, and bots gain a
backward-leap disengage from melee scrums.

### Cloud sync — uploadPlugin error fixed
The "Save error: uploadPlugin.upload is not a function" was caused by
calling the wrong API shape — earlier code tried `uploadPlugin.upload`,
`uploadPlugin.uploadFile`, `uploadPlugin.uploadString` as fallbacks.
Per the Perchance documentation, `uploadPlugin` is imported as a
single callable: `await uploadPlugin(blob)` returns `{ url, size,
error }`. Fixed by stripping all the fallback shapes and calling
the documented signature directly. Save now works.

### Two new positions — Healer + Grenadier
Roster size grew from **5 → 7**. Each team now fields:

- **Tank** 🛡️ — armor wall (unchanged)
- **Striker** ⚔️ — balanced damage dealer (unchanged)
- **Speedster** 💨 — fast skirmisher (unchanged)
- **Brawler** 👊 — melee tank-lite (unchanged)
- **Sniper** 🎯 — ranged glass cannon (unchanged)
- ✨ **Healer** ✚ — support unit (NEW)
- 💣 **Grenadier** 💣 — area-of-effect specialist (NEW)

#### Healer 🟢
Doesn't fight. Picks a "patient" from their team — whoever is
lowest-HP — and stays in heal-beam range (~70-90px). Re-evaluates
patient every 3 seconds, switching to new lowest-HP teammate. Has a
**minimum range** (25px), so they physically follow *behind*
whoever they're healing rather than crowding into the front line.

The heal beam connects when the patient is in line-of-sight and
restores ~3 HP/sec. Visualized as small "✚" particles flickering
along the line between healer and patient.

Default loadout: **Healing Wand** (✨, 90px range, no offensive
damage) + **Medic Robe** (🥻, +2 DEF, +0.2 SPD). Modest stats.

**Snipers prioritize healers as targets** — even if a closer non-
healer enemy exists, snipers within range will pick the nearest
healer first. This makes healer placement strategically important.

#### Grenadier 🟠
Throws **one grenade at the start of an engagement** with a long
~8-second cooldown, then defaults to weak melee for the rest of
the round. The grenade is a slow projectile that explodes on
impact, dealing **AoE damage** to all enemies within 60px. Damage
falls off with distance from blast center.

Grenadiers position 120-180px from the nearest enemy (grenade
range) and back off if pushed inside that distance. They're
fragile in sustained combat — the team gets one good opening
explosion, then the grenadier is mostly along for the ride.

Default loadout: **Grenade Launcher** (💣, 220px range, 6 projSpeed,
long cooldown) + **Flak Vest** (🦺, +4 DEF). Grenade hitting a wall
detonates there. Camera shake fires on every explosion.

### Dodge tuning (three bug fixes)

1. **Tanks never dodge.** Previously the dodge formula scaled with
   SPD; tanks have low SPD but were still dodging occasionally.
   Now hard-gated: `if (p.position === "tank") return false`.
   Verified 0/200 dodges in synthetic test.

2. **Snipers dodge much less.** Snipers have decent SPD but were
   already dominant; giving them dodge tipped balance further. Now
   their effective `dodgeSkill` is multiplied by 0.45. Verified
   striker 80 dodges vs sniper 44 dodges in matched-SPD test (45%
   reduction).

3. **Dodge cooldown added.** A successful dodge now sets
   `_dodgeCdUntil = m.frame + cd` where `cd` scales with SPD
   (~9 frames for speedsters, ~22 for striker, ~26 for sniper).
   No more spam-dodging through projectile clusters.

Healers and grenadiers also get reduced dodge skill (×0.85 of
the striker baseline) since they aren't combat-mobile by design.

### NEW: Backward dodge from melee scrums
A `tryDisengageDodge` runs alongside the projectile dodge. Fires
when a non-tank bot is within 30px of an enemy AND any of:

- The bot wields a **ranged weapon** (laser, railgun, launcher,
  wand) and is stuck in melee
- Bot is below **35% HP**
- Bot is a **sniper, healer, or grenadier** (always prefers
  distance from melee)

The bot leaps backward away from the nearest enemy at 1.05× speed
with a separate ~22-frame cooldown. Visualized as a "←DASH" float.
Verified 63% disengage success rate when a sniper is shoved into
melee in the synthetic test.

### Verified end-to-end
- All 7 positions present in every team's roster, all 12 teams
- Healer heals teammate from 109 → 177 HP across 200 frames (~10s)
- Grenadier launches grenades; blast floats observed; camera shakes
- Tanks: 0/200 dodges in synthetic test
- Sniper 44 vs Striker 80 dodges with same SPD
- Sniper disengages from melee 63% of opportunities
- Determinism preserved across all changes (3-0 wlf for seed 0xDEAD11)
- localStorage save/reload roundtrip still clean (7-roster format)

## v19 patch — Healer + Grenadier roles, dodge restructure, layout polish

### Two new archetypes — Healer and Grenadier
The original 5-knight roster (Tank / Striker / Speedster / Brawler /
Sniper) expanded to **7 knights per team**. Both new positions have
their own behavior systems and visual markers.

**✚ Healer** — Support unit. Doesn't shoot enemies; instead heals the
lowest-HP teammate over time via a heal-beam. Has minimum range, so
it physically follows behind whoever it's healing — sometimes
wandering across the battlefield when its heal target moves or
swaps. **Snipers preferentially target Healers** in their own
target-selection: even if a closer non-healer enemy is in range, a
sniper will pick the nearest healer instead. Modest stats overall;
the healer-on-the-team is a force multiplier, not a fighter.

**💣 Grenadier** — Throws ONE grenade at the start of an engagement
(huge cooldown ensures it's effectively single-use per match). The
grenade is a slow projectile that explodes on impact, dealing AoE
damage to enemies in range. Afterwards, the Grenadier falls back to
weak melee — bring 'em early, then they're a liability.

Each team now has exactly one of every archetype. Verified all 12
teams have full 7-knight rosters with one of each position type.

### Dodge mechanic restructured
v18 shipped projectile-dodging but had three issues. All three fixed:

- **Tanks now don't dodge at all** — hard return false. Tanks are
  built to soak hits; dodging undermines their identity.
- **Snipers dodge ~45% of normal frequency** — they're already
  overpowered with their range advantage, so balance forced them
  down. Per-bot dodge skill is now position-aware, not just
  SPD-derived.
- **Per-bot dodge cooldown** — once a knight dodges, they can't
  dodge again for ~8-24 frames (scaled inversely by SPD; speedsters
  recover fastest). Dodging becomes a punctuation, not a constant
  weave.

### Backwards-disengage dodge (NEW)
When a knight is in melee range of their target AND below 50% HP,
the dodge logic now picks **backwards** (away from the melee
threat) rather than perpendicular to the projectile. This gives
bots a way to break out of close-quarters scrums without waiting
for the FLEE state to take over. Visual tell uses **↩** (gold)
instead of **↯** (cyan) so you can see the difference.

Verified: in 100 trials of "speedster, low HP, target in melee
range, projectile incoming" — **43/43 dodges that fired went
backwards**, zero perpendicular. Out of melee range or full HP,
the standard perpendicular sidestep takes over.

### Layout: League Legends moved to League tab
The "LEAGUE LEGENDS" and "LEGENDARY LOST LOOT" sections previously
lived in the Settings tab — wrong place, since they're content
about the league's history. Both now live in the **League** tab
between the live "Hall of Fallen Heroes" and "Biggest Loot Losses"
sections.

The mixed presentation (where canon legends were also injected into
the live Hall and Loot lists with ★ LEGEND badges) is now split:
the live sections only show your league's actual entries, and the
dedicated Legends sections show only the canonical pre-seeded
content. Less redundancy, clearer separation.

### Cloud Sync bug fixed
Earlier code tried `uploadPlugin.upload(blob, filename)` which
isn't the actual Perchance API — `uploadPlugin` is imported as a
single CALLABLE function. Now correctly calls
`await uploadPlugin(blob)` with a Blob (JSON wrapped with the
`application/json` MIME type) and reads `{url, size, error}` from
the result. Should resolve the
"uploadPlugin.upload is not a function" error.

### About tab rewrite
The About tab content was dramatically out of date — it described
"4 teams of 5 knights" with a 2024-vintage roadmap and no mention
of any of the post-v9 features. Rewritten from scratch to reflect
v19 reality: 12 teams / 7 archetypes per team / dodging / Power
Core / death cries / scavenge lineages / 6-market betting / shop
rarity tiers / League Legends. New section explicitly walks through
all seven archetypes and their roles.

### Round duration recovered
v18's dodge mechanic had inadvertently shrunk rounds from the v15
target of ~45s down to ~18s (the perpendicular weave was
accelerating melee convergence). With v19's tightened dodge
constraints (position gating, cooldown, smaller nudge magnitude
when in melee, larger nudge only when escaping melee) the average
is back to **41.4s across 6 matches with the v15 baseline seed**
— a ~96% recovery to the target band.

### Verified end-to-end
- All 12 teams have 7-knight rosters with one of each archetype
- Tanks dodge 0/100 trials; backwards-disengage 43/43 in melee+lowHP
- Settings tab no longer contains legends grid; League tab contains
  both new sections in the right structural position
- Determinism preserved (seed `0x999333` reproduces identical match)
- Round duration 41.4s avg (target ~45s), back from v18's 18s regression

## v20 patch — AI Sportscaster Commentary system

### What's new
A dedicated **sportscaster commentary** panel sits above the kill feed
in the arena, calling the action like a real broadcast. Lines are
short, distinct in tone from the dry kill log, and styled with a gold
border to mark them apart visually.

### Architecture: static foundation, AI enhancement layer
The system has **two tiers**, layered:

**Tier 1 (always on)**: rich static phrase pools fire instantly. No
latency, no plugin dependency, no failure modes. The pools are
broken down by event type:

- `COMM_MATCH_OPEN` (8 lines) — fires when a match begins
- `COMM_FIRST_KILL` (5 lines) — fires when the first kill of a match
  happens
- `COMM_STREAK_BIG` (4 lines) — fires at 5/10/15/25 kill streaks
- `COMM_LAST_KNIGHT` (4 lines) — fires when a team is reduced to one
  alive knight
- `COMM_MATCH_WIN_DOM` (3 lines) — match-end, dominant wins (margin ≥ 3)
- `COMM_MATCH_WIN_CLOSE` (3 lines) — match-end, close wins (margin < 3)
- `COMM_MATCH_DRAW` (3 lines) — match-end, draw → coin flip
- `COMM_CORE_CAPTURE` (3 lines) — fires on Power Core capture

Lines are interpolated through `ccPick(pool, vars)` which substitutes
`{a}`, `{b}`, `{p}`, `{tm}`, `{n}`, `{sa}`, `{sb}` etc. — so the same
phrase template covers any team / knight / score combination.

**Tier 2 (when ai-text-plugin is present)**: an enhancement layer
fires asynchronously after each call to `commentate()`. If the plugin
returns a better line, it swaps in and re-renders. The static line
shows immediately; the AI line replaces it if it arrives. The user
never waits.

The AI hook implementation is defensive against plugin API variation —
tries `aiTextPlugin(prompt)` (callable), then `.getResponse(prompt)`,
then `.generate({instruction})`. Caches by event-type + name so
repeated identical events (e.g. "Sammy first-killed someone again
this season") don't re-prompt. Rate-limited to 1 request per 1500ms
to prevent burst overuse.

### Pre-match team chants
Each of the 12 teams now has a `chants` array — three short shouted
lines per team that fit their personality. When a match starts, both
teams' chants fire as commentary entries with a distinct gold-italic
style. Examples:

- **Foxes** (mottos: "Burn first, ask later"): "BURN FIRST!" / "FUR ON FIRE!" / "FOXES OVER ALL!"
- **Beavers** (motto: "Build it, then break it"): "BUILT BY BEAVERS!" / "WE BUILD! WE BREAK!" / "DAM RIGHT!"
- **Pandas** (motto: "Calm hands, heavy fists"): "BAMBOO!" / "CALM. HEAVY. PANDA." / "SOAK IT UP!"
- **Wolves** (motto: "Hunt as one"): "AWOOOO!" / "HUNT AS ONE!" / "PACK TACTICS!"

### Visual treatment
The sportscaster panel is styled separately from the kill feed:

- Gold-bordered header with 🎙️ icon
- Different colors for each line type: chants gold-italic, openers
  gold-bold, hype play-by-play yellow, sad lines purple-italic, color
  commentary cyan-italic, deadpan defaults to ink
- Most-recent line gets a soft gold highlight that fades
- AI-enhanced lines display a small "AI" badge so you can tell which
  came from the static pool versus the live model

### perchance-top.txt update
Added `aiTextPlugin = {import:ai-text-plugin}` to the imports list
in `perchance-top.txt`. **You'll need to redeploy this to Perchance**
for the AI enhancement layer to activate. Without the import, the
static phrase pool is the entire commentary system — which is itself
sufficient for shipping.

### Verified
- All 12 teams have 3-chant arrays
- Match start fires 1 opener + 2 chants (one per team)
- Play-by-play firing throughout matches (29 lines in a sample run)
- Match-end commentary with dominant/close/draw branching
- AI hook is a no-op when ai-text-plugin isn't loaded — no errors
- Determinism preserved (seed `0x999333` reproduces identical outcomes)
- Round duration in target band (27.9s avg across 5 matches)
- Smoke test passes

## v21 patch — Biome system + atmospheric effects

### Five biomes with deterministic per-match selection
The arena floor is no longer a single dark gradient. Each match
picks one of **5 biomes** based on the match seed (so the same
match always renders the same look):

- **🌱 Stadium Pitch** — green grass with dandelions, mowing stripes,
  small pebbles. The default sports-broadcast look.
- **🏜️ Desert Pit** — sandy floor with cacti, sand-blown wisps, scattered
  pebbles. Warm orange vignette.
- **❄️ Frozen Rink** — pale blue ice with cracks, snow tufts, dark pebbles.
  Cool blue sky tint.
- **🌋 Volcanic Lot** — dark gray rock with glowing embers and lava cracks.
  Dramatic red tint.
- **🌸 Cherry Blossom Garden** — soft green pitch with grass tufts and
  drifting pink petals. Twilight vignette.

The biome name and emoji appear in the matchup header at the top of
the arena so you always know which field you're watching.

### Per-biome decorative scatter
Each biome has its own scatter palette — small sprite types unique
to that biome, scattered across the field at deterministic positions.
A typical match has ~150-200 scatter sprites: grass tufts (3-blade
fan with highlight), dandelion puffs (yellow center + white satellites),
sand wisps, cacti, ice cracks, snow dots, glowing embers, lava cracks,
or pink petals depending on the biome.

The scatter avoids:
- The 75px center keep-out where the Power Core spawns
- The spawn columns at the left/right 80px (mostly — some allowed for
  decoration)

### Cloud shadows passing overhead
2-3 soft semi-transparent dark patches drift diagonally across the
field per match. Each has its own size, drift velocity, and alpha,
generating a subtle "broadcast on a partly-cloudy day" feel. They
wrap around the arena edges so the field always has at least one
cloud on it.

### Player shadows upgrade
The existing single-ellipse shadow under each knight is now a
**two-layer soft shadow** — a wider 18% alpha halo + a tighter 45%
core, slightly offset down-right to suggest broadcast lighting from
above-left (matching the vignette direction). The mascots now read
as genuinely floating above the field rather than pasted onto it.

### Field markings
- Penalty arc at each end (90px radius semicircle) — classic football
  pitch detail
- Team mottos as ground text — each team's short name embossed faintly
  on their own end zone (vertical orientation, 22% alpha team color)
- Field lines are now biome-aware — accent color comes from each
  biome's `accents.lineColor`

### Independent RNG stream for biome generation
The biome system uses a **separate RNG instance** (`BIOME_RNG`) seeded
from the match seed XORed with `0x9E3779B9`. This means biome variety
is fully deterministic per match (same seed → same biome + same scatter
positions) but the biome generation **never consumes RNG numbers from
the combat stream**. Adding or removing decorations in a future patch
will not affect any past or future match outcomes.

Verified: seed `0x999333` produces the same `0-3 frg` match outcome
as it did in v20 — combat determinism preserved end-to-end.

### Verified
- All 5 biomes have valid schema (id, name, emoji, colors, scatter, accents, sky tint)
- Match init creates biome + 172 avg scatter sprites + 2-3 cloud shadows
- Center power-core zone has 0 decorations encroaching
- Cloud shadows drift over time (verified 8px movement in 30 frames)
- Same seed produces identical biome + scatter signature across re-runs
- All 5 biomes appear across 50 random seeds (good variety)
- Each biome's scatter contains exactly its own sprite type set
- Match outcome determinism preserved (seed 0x999333 still 0-3 frg as in v20)