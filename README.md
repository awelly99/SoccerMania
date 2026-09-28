# wwwroot/models/stadium — 3D venue assets

> Last updated: 2026-09-23

WHAT: the `.glb` models served to the browser for the **"3D Stadium (studiolab)"** venue
(venue id **5**), plus `manifest.json` which names them.

WHEN: fetched once by `wwwroot/js/stadium3d.js` when a match starts on that venue.

**STATUS: exported and live (2026-09-23).** The purchased Unity pack has been imported and
converted; the renderer now draws the real studiolab stadium instead of the procedural
placeholder.

## Files

| File | Contents | Manifest key | Tris | Bytes |
|---|---|---|---:|---:|
| `stadium_shell.glb` | stands, roof, floodlights, seats, glass, signage, LED banners | `models.stadiumShell` | 247 689 | 12 655 740 |
| `pitch.glb` | ground plane + painted line decals | `models.pitch` | 3 286 | 168 812 |
| `goals.glb` | both goal frames (nets are simulated by the renderer) | `models.goals` | 5 146 | 124 860 |
| `ball_a.glb` / `ball_b.glb` | the two footballs (reserved — the match ball is a billboard sprite today) | `models.ballA` / `models.ballB` | 3 072 / 2 880 | 56 384 / 63 028 |

**None of them is required.** Every model the manifest leaves `null` — or whose file returns
404 — is replaced by a procedurally generated equivalent built to the same dimensions, so the
venue still renders (stands, floodlights, painted lines, goals + simulated nets, corner flags).

## How they are authored

The geometry is **baked into the renderer's world units** — centre spot at the origin, Y up,
+X along the pitch length, painted playing area exactly `±PitchWidthPx/99/2` by
`±PitchHeightPx/99/2` — because `stadium3d.js → loadGlb()` applies no scale or yaw of its own.
`manifest.json → pitchModel.*CornerWorld` therefore stays `null` (worldScale 1, yaw 0,
offset 0). They carry `baseColorFactor` materials and **no textures, no Draco, no KTX2**.

Do not hand-edit these files: re-run `tools/UnityStadiumExport/export.ps1`, which regenerates
them and prints the byte sizes to paste into `manifest.json → exportStats`.
`SoccerMania.Tests/Stadium3DModelAssetTests.cs` fails if the two ever disagree.

## Serving

`.glb` has no default ASP.NET Core MIME mapping, so `SoccerMania/Program.cs` registers
`model/gltf-binary` on the static-file provider. Without it every request 404s and the
renderer silently falls back to the procedural stadium.

## Licence

Derived from **Unity Asset Store "Football / Soccer Ground" by studiolab**, Standard Unity
Asset Store EULA, *Single Entity* seat. Only **derived/exported** files may be placed here.
Never commit or serve the original `.fbx` / `.psd` / `.unitypackage` sources.

See `tools/UnityStadiumExport/README.md` for the full export + bake pipeline.
