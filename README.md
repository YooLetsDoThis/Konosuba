# Konosuba — resource pack

The companion resource pack for the **KONOSUBA** datapack: custom item models (Master Sword, Megumin's Hat/Staff, the Magic Canceller "ancient" scroll, …) and all custom sounds, including Megumin's **Introduction of the Crimson Demons** voice line.

## Contents
- `pack.mcmeta`, `pack.png`, `assets/` — the editable resource-pack source.
- `Konosuba.zip` — the built pack, ready to host and serve as the server resource pack.

## Server use (forced resource pack)
The Minecraft server applies this automatically on join. In `server.properties`:

```properties
resource-pack=<DIRECT DOWNLOAD URL to Konosuba.zip>
resource-pack-sha1=bc7106912ae283638afc0ae42dd3305582eacb59
require-resource-pack=true
```

> **Heads up:** this repo is **private**, so GitHub's `raw`/release links here require auth — a Minecraft client can't download from them. To actually *serve* the pack to players, either make this repo **public** (then the raw URL to `Konosuba.zip` works) or host the zip on a public direct link (e.g. Dropbox `?dl=1`) while keeping this repo as the private source of record.

## Updating
1. Edit files under `assets/` (or replace `Konosuba.zip`).
2. Rebuild the zip and recompute the SHA-1:
   ```powershell
   Compress-Archive -Path pack.mcmeta,pack.png,assets -DestinationPath Konosuba.zip -Force
   (Get-FileHash -Algorithm SHA1 Konosuba.zip).Hash.ToLower()
   ```
3. Commit + push, re-host the zip, and put the new SHA-1 in `server.properties`, then restart the server.
