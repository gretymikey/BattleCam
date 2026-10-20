# BattleCam

iPhone-only, landscape, bodycam-style tactical FPS. SwiftUI front end over a custom Metal engine ("MX"). Plays offline against bots. Built unsigned via XcodeGen + GitHub Actions.

Built by MRzefV.

## Build

1. Push to `main` (or run **Build IPA** from the Actions tab).
2. Download the `BattleCam-ipa` artifact: an unsigned `BattleCam.ipa`.
3. Sign and install it with your usual signer.

On failure the run summary lists the compiler errors. The full log is in the `build-log` artifact and in `build-logs/errors.txt` on the `build-logs` branch.

## Layout

- `project.yml`: XcodeGen spec (bundle `city.battlecam.game`, iOS 16+, iPhone)
- `.github/workflows/build.yml`: CI. Its `keep=` list prunes stale Swift files, so **add every new `.swift` file to it**.
- `Sources/`: game and engine
  - `MX*.swift`: Metal engine (loader, renderer, shaders, materials)
  - `Tactical*.swift`, `Tac*.swift`: game logic, maps, HUD, weapons, lobby
  - `TacAnnex.swift`: ANNEX, the CQC two-floor map with continuous stairs (default)
  - `TacFX.swift`: bullet decals, dust, sparks, blood, glass
  - `TacMuzzle.swift`: muzzle flash
- `Resources/`: gun models (`gun_*.glb`, `gunmeta.json`), Annex baked maps (`annex_f1.glb`, `annex_f2.glb`), fonts, fx
- `Assets.xcassets/`: icons and images

## Maps

ANNEX (default): compact CQC house, 1000x800 lot, 10 rooms per floor, 2-3 doors on every room, one continuous staircase. BLOCK 9: large. CUSTOM: editor map. Pick one in the lobby's OPERATION card.

## Engine notes

- The Annex is modelled in Blender and baked in Cycles (albedo, light and AO, height). Materials load as baked, so the engine skips its own wall and floor lighting tricks on them.
- Post: ACES tone mapping, bloom, adaptive exposure, lens dirt, bodycam grade.
- Dynamic: 8 nearest point lights, muzzle-flash light, sun shadow map.
- Not implemented: SSAO (needs a stored depth buffer).

## Controls

Fixed move stick (bottom-left). Drag the right side to free-look (up, down, left, right). FIRE button for manual shooting (no auto-fire, no aim assist). ADS toggle raises the weapon so its sight anchor sits on the camera axis with the barrel levelled, slows look sensitivity and tightens spread. No crosshair. Reload, crouch, jump.

## Bots

Every bot has its own traits (aggression, patience, reflex, nerve) and picks its own posts, so they spread out instead of moving as squads.

## Status

The latest changes have not been compiled on device. If CI fails, send the `errors.txt`.

## Operators
Operators are modular: `Sources/TacGear.swift` defines 8 kits (Assault, Breacher, Recon, Marksman, Medic, Support, Spectre, Urban). Each kit sets uniform, rig, boots, gloves, helmet, face covering, eyewear, back items and body bulk (silhouette). Customize → Operators lets you swap face covering, helmet, chest rig, gloves and eyewear per slot, and recolour with Skins. First-person sleeves and gloves follow the selected kit (arms are exported as separate `glove`, `sleeve` and skin meshes). Bots each get a kit from their handle.
