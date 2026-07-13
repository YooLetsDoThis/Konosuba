# Konosuba — resource pack (custom sounds)

Companion resource pack for the **KONOSUBA** datapack. Holds the custom audio the datapack
triggers with `/playsound` (Explosion music, Steal, Jackpot, …).

- **Target:** Minecraft **1.21.8** (Fabric). `pack_format` 64, `supported_formats` 55–99.
- **Type:** loaded as a **folder** pack locally (Minecraft reads it straight from `resourcepacks/`), but
  **shipped to players as a zip** off a regular dedicated server — see [Server deployment](#server-deployment-regular-dedicated-server).
- **Namespace:** `konosuba` (custom sound events live under `assets/konosuba/`).

## How to install / load
1. It already lives in this instance's `resourcepacks/` folder.
2. In game: **Options → Resource Packs → move "KONOSUBA custom sounds" to Selected**, then **Done**.
3. After editing `sounds.json` or adding `.ogg` files, press **F3 + T** to reload resources
   (no game restart needed). For players on the server, publish a new zip — see [Server deployment](#server-deployment-regular-dedicated-server).

## Server deployment (regular dedicated server)
The pack runs on a **regular dedicated Minecraft server** (not an Essential-hosted world). The server
auto-pushes it to every client on join via `server.properties`:

```
resource-pack=https://raw.githubusercontent.com/YooLetsDoThis/Konosuba/main/Konosuba.zip
resource-pack-sha1=<SHA-1 of the .zip FILE>
```

> ⚠️ **`resource-pack-sha1` is the SHA-1 of the zip file's bytes — NOT a git commit hash.** Get it with
> `sha1sum Konosuba.zip` (or `certutil -hashfile Konosuba.zip SHA1`). A git commit SHA-1 will never match
> the downloaded file, and clients show **"Failed to load."** (That exact mix-up happened once — the git
> commit hash got pasted into `resource-pack-sha1`.)

**To publish an update after changing sounds:**
1. Rebuild `Konosuba.zip` from this folder with `assets/`, `pack.mcmeta`, `pack.png` at the **zip root**
   (no wrapping `Konosuba/` folder; exclude `.git/` and `README.md`).
2. Upload it as `Konosuba.zip` to the **`main`** branch of the `YooLetsDoThis/Konosuba` repo (the raw URL above serves those exact bytes).
3. Put the new `sha1sum` into `resource-pack-sha1` and **restart the server**. (`raw.githubusercontent.com`
   caches ~5 min, so the new zip may take a moment to propagate; clients cache by hash, so a changed hash forces a fresh download.)

**Current build:** SHA-1 `85deac04e79b1942bb9225fdbee179eb78ba8701` — adds three battle tracks:
`vault.music` (Temple of Time — loops in the vault near the Master Sword pedestal), `boss.music`
(ELEISON by Naktigonis — the meadow finale, starts on Kazuma's speech), and `boss.blademaster`
(The Windsinger's Dance by Naktigonis — starts when the Yiga Blademaster spawns). On top of `outro.music`
(`outro/music.ogg` — "I" by BUMP OF CHICKEN, the end-credits theme, 96.1s stereo, `master` cat), Master Sword
(song/pull/cancel), Warp (in/out/tower), Cryonis (form/crush), Urbosa's Fury (activate/lightning), and the rune
install; plus `particle` textures added to the `megumin_hat`/`megumin_staff` models (silences a benign load warning).

## Sound files — where they go & format rules
All audio must be **Ogg Vorbis (`.ogg`)** — MP3/WAV will silently fail to load. Convert with
Audacity (`File → Export → Export as OGG`) or `ffmpeg -i in.mp3 out.ogg`.

| Event (`/playsound …`)        | File path under `assets/konosuba/sounds/` | Notes                          |
|-------------------------------|--------------------------------------------|--------------------------------|
| `konosuba:explosion.music`    | `explosion/ExplosionMusic.ogg` ✅ present   | trimmed 24.68s theme (0–11.7s + 57–70s), `player` cat, stereo |
| `konosuba:explosion.boom`     | `explosion/ExplosionBoom.ogg` ✅ present    | blast SFX at detonation, loudness-maxed (~+7 dB), full ~7s, `player` cat |
| `konosuba:steal`              | `steal/steal.ogg` ✅ present                | OST 38 "Steal" sting (~5s, trailing silence trimmed), `player` cat; plays the instant he presses X on a normal steal |
| `konosuba:jackpot`            | `jackpot/jackpot.ogg` ✅ present            | Kazuma "No way…JACKPOT!" (~7.4s), `player` cat; plays on press when the mark is holding a `#konosuba:treasure` item |

- **Mono vs stereo:** mono files play **positionally** (3D, fade with distance). Stereo files play
  **flat/2D** no matter where they're emitted. Use mono for in-world SFX, stereo for music/UI stingers.
- Placeholder `PUT_*.ogg_HERE.txt` files mark each folder — delete them once the real `.ogg` is in.

## Adding a new sound event
1. Drop `myfile.ogg` at `assets/konosuba/sounds/<folder>/myfile.ogg`.
2. Add an entry to `assets/konosuba/sounds.json`:
   ```json
   "my_event": {
     "category": "player",
     "subtitle": "konosuba.subtitle.my_event",
     "sounds": [ "konosuba:<folder>/myfile" ]
   }
   ```
   (`category` picks the volume-slider mixer: `music`, `record`, `player`, `hostile`, `ambient`,
   `block`, `voice`, `master`. The `name` is the file path **without** `.ogg`.)
3. (optional) Add the subtitle text to `assets/konosuba/lang/en_us.json`.
4. Trigger it from the datapack:
   ```
   playsound konosuba:my_event player @a[distance=..30] ~ ~ ~ 1 1
   ```
   Stop a long one (e.g. the Explosion music) with:
   ```
   stopsound @a record konosuba:explosion.music
   ```
   > `<source>` in `/playsound`/`/stopsound` must match the event's `category`. **Avoid the `music`
   > category for must-be-heard sounds** — players (and this dev) often set the Music slider to 0.

## Free-use palette — Nether & End ambience
The target server has **no Nether or End ambience of any kind**, so every Nether/End ambient and
music event is safe to **repurpose**. These are already-registered vanilla events, so you can either
(a) keep using the clean `konosuba:` events above, or (b) override any of the below by adding them to
`assets/minecraft/sounds.json` (that file **merges** per-event with vanilla — listed events are
replaced, everything else is untouched). Useful when you want a sound to ride the vanilla music/ambient
system or avoid bloating the custom namespace.

**Per-biome ambience** — each has `.mood`, `.additions`, and `.loop`:
- `minecraft:ambient.nether_wastes.{mood,additions,loop}`
- `minecraft:ambient.soul_sand_valley.{mood,additions,loop}`
- `minecraft:ambient.crimson_forest.{mood,additions,loop}`
- `minecraft:ambient.warped_forest.{mood,additions,loop}`
- `minecraft:ambient.basalt_deltas.{mood,additions,loop}`

**Music:**
- `minecraft:music.nether.nether_wastes`
- `minecraft:music.nether.soul_sand_valley`
- `minecraft:music.nether.crimson_forest`
- `minecraft:music.nether.warped_forest`
- `minecraft:music.nether.basalt_deltas`
- `minecraft:music.end`
- `minecraft:music.dragon` (ender-dragon fight music)

To repurpose one, e.g. route the Steal stinger onto a nether mood slot, add to
`assets/minecraft/sounds.json`:
```json
{
  "ambient.nether_wastes.mood": {
    "replace": true,
    "sounds": [ "konosuba:explosion/ExplosionBoom" ]
  }
}
```
(`"replace": true` drops vanilla's entries for that one event so only your sound plays.)

## Explosion theme — single seamless track + cancel cooldown
The whole theme is **one file** (`konosuba:explosion.music` → `explosion/ExplosionMusic.ogg`), played
once in full by the datapack. No segmenting — that was tried (35 × 2s parts for a scripted fade) and
**scrapped**: re-encoded Ogg parts clip badly at every join, with no gapless support in Minecraft.

Wiring (datapack `megumin/explosion/`):
- **Start:** `cast_begin` plays the full track the instant the cast activates (a too-close refusal never
  reaches it), to **everyone in her chant/subtitle range** (`@a[distance=..100]`, centered per-listener
  for full volume). A `stopsound` first prevents overlap if she casts again.
- **Detonation:** the theme plays out (aftermath) and `konosuba:explosion.boom` (the blast SFX, loudness-
  maxed, full-length) fires from the impact point the instant the blast resolves (`fire.mcfunction`),
  reaching **256 blocks** — farther than the theme/chant (`..100`) so it carries on far-away blasts.
  `meg_weary` is the cooldown.
- **Cancelled/failed chant:** the full track **restarts** as the lockout soundtrack and `meg_nocast` is
  set to the **track's length** so she can't re-cast until it ends. ⚠️ That value (in `chant_abort`,
  currently `494`t = 24.68s, matching the trimmed theme) **must match `ExplosionMusic.ogg`'s actual
  length** — tell Claude the length when you swap the file and it'll update it.

> **Category = `player`, NOT `music`.** The Music slider is commonly set to 0 (it was here — that's why
> the theme was silent at first). The theme + boom play on the **`player`** (Players) category, which is
> almost never turned down. The `/playsound` source and `sounds.json` category must agree. Everything is
> still scaled by the **Master** slider, so a low Master makes it quiet too — a global preference, not a
> pack issue.

> **TNT sound muted PER-SPELL, not globally.** The crater is carved by real `fuse:0` TNT (kept — it
> looks right and throws debris), and the datapack kills its blast noise with a **per-tick `/stopsound`**
> while the blast is live: `stopsound @a[distance=..100] block minecraft:entity.generic.explode` in
> `process`. Only that one sound, only near the crater, only during the spell — so creepers/TNT elsewhere
> stay audible (no global `assets/minecraft/sounds.json` mute). Caveat: `/stopsound` is reactive (cuts a
> sound *after* it starts) and runs once per tick, so each tick's TNT can leak a ~50ms crack before it's
> cut; the loud boom largely masks it. Vanilla has no preemptive per-window sound toggle, so this is as
> clean as scoped gets.

## TODO
- [x] `explosion/ExplosionMusic.ogg` wired as the single seamless theme (`player` category, trimmed to 24.68s).
- [x] `ExplosionBoom.ogg` wired at detonation, loudness-maxed (~+7 dB), full-length, reaches 256 blocks.
- [x] Carve-TNT blast noise killed per-spell via per-tick `/stopsound` (no global mute; other explosions intact).
- [ ] If you swap in a different-length track, tell Claude so the cancel cooldown (`meg_nocast`) is updated.
- [x] Steal sting wired (`konosuba:steal`, OST 38 "Steal" 1:19→end) → plays on a successful steal (`kazuma/steal/announce`).
- [x] Jackpot sting wired (`konosuba:jackpot`) → plays instead of the Steal sting when he grabs a `#konosuba:treasure` item.
- [ ] (optional) `pack.png` — a 128×128 icon shown in the resource-pack list.
