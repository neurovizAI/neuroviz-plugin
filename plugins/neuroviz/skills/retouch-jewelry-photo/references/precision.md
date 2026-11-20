# Jewelry Retoucher Precision — Agent Guide

_Reach for this app when the source photo is sharp and well-lit AND the piece itself is clean and undamaged, and the goal is maximum-fidelity catalog retouching at up to 16 Mpx._

## What it does — and what it will NOT do

**Does:** High-fidelity retouching of professionally photographed, physically clean jewelry. It polishes metal, enhances lighting, and preserves fine engraving, prong texture, and gemstone facets exactly as photographed. Output is a clean product image (white/solid/transparent background options) at up to 16 Mpx native resolution — no upscaling. Accepts all jewelry types including watches and timepieces (dial close-ups, full watch shots, watch-and-strap photos). Handles two-tone pieces via up to 3 custom reference colors.

**Will NOT do:**
- Repair or remove physical imperfections — deep scratches, dents, tarnish, oxidation, dirt, heavy fingerprints stay visible. That is by design: its goal is faithful precision, so a worn or dirty piece comes back worn and dirty.
- Reconstruct blurry, soft, dark, or low-resolution photos. It preserves what is in the source; soft areas stay soft, and low input quality caps output quality.
- Extract jewelry from an on-model or lifestyle photo, place jewelry on a model, build creative scenes, or make structural design changes (swap stone types, add/remove elements, resize, reshape).
- Follow generic retouching prompts ("polish", "add sparkle", "don't change anything") — it retouches automatically, and duplicate instructions actively degrade output.

## Choose it / don't choose it

Choose Precision **only when both are true**: the photo is sharp, well-lit, high quality, AND the piece is clean and in good condition. If either fails, pick a different app:

| Don't choose Precision for... | Use instead |
|---|---|
| Worn, scratched, tarnished, oxidized, or dirty pieces | **Jewelry Retoucher Rescue** — cleans and recovers regardless of condition |
| Blurry, dark, soft-focus, noisy, or low-resolution photos | **Jewelry Retoucher Rescue** — built to reconstruct rough sources |
| Richer, saturated marketing look (deeper shadows, stronger contrast) | **Jewelry Retoucher Vivid** — Precision stays natural and true to the piece |
| Jewelry on a model / try-on | **Rings Try On**, **Earrings Try On**, **Necklace Try - On**, **Bracelet Try-ON**, or **Watch Try-On** |
| Creative scenes or styled backgrounds | **Creative Jewelry Photographer Vivid** |
| Structural design changes (different stone type, add/remove stones, resize) | **Jewelry Modifier Pro** (metal/stone COLOR changes are fine here via reference colors) |
| Minimal or no enhancement, fine control over processing strength | **AI Photo Editor Pro – Retouching & Editing V2** |
| Targeted tweaks to an already-retouched or rendered image | **Jewelry Modifier Pro** — a full re-retouch of a rendered image looks artificial or "painted" |

Two-question quick-check before submitting here:
1. Is the photo sharp, well-lit, ≥2000 px? If no -> **Jewelry Retoucher Rescue**.
2. Is the piece itself free of scratches, tarnish, oxidation, dirt? If no -> **Jewelry Retoucher Rescue**.
Only a yes to both makes Precision the right call; a rough gold ring on a decent photo still belongs in Rescue (confirmed support outcome — Precision returned it with the flaws intact).

## Inputs that make or break the result

- `input_image_1` must be a publicly reachable HTTPS URL. Everything in focus, good-quality photography; **2000×2000 px minimum recommended** — at HQ tiers, source detail is what gets preserved, so soft input wastes the resolution.
- **Crop tight before hosting.** If the piece is small in the frame with lots of empty background, high-res output wastes pixels on background, and loose framing is the top cause of position/orientation drift in results. Pass a tightly cropped image URL.
- **Rotate before hosting.** If the piece is sideways or upside down in the source, fix orientation in the file and pass the corrected URL — the output mirrors source orientation.
- **One piece per run.** Multiple unrelated items give inconsistent results; crop to a single subject or run pieces separately.
- **Full piece in frame.** Anything cut off at the source edge stays cut off. A hand covering part of the piece means the hidden area may not retouch cleanly.
- **Neutral, even lighting.** Colored light, colored surfaces reflecting into metal, or a very dark exposure shift metal color; the fix is a better-lit source or explicit reference colors.
- **Clean the piece physically before shooting.** Precision keeps surfaces as shot — dust and debris in channels or prongs stay (or, as with any retoucher, bright specks can be misread as tiny stones).
- Product shots only — never an on-model/lifestyle photo.

## Parameter playbook

Exact names and enum values (schema is authoritative; copy enum strings exactly, case included):

- `input_image_1` (image_url, **required**) — HTTPS URL string.
- `model_tier` (string, default `"Retoucher SD - 1Mpx"`): `"Retoucher SD - 1Mpx"` (1024×1024) | `"Retoucher PRO (HQ) - 4Mpx"` (2000×2000) | `"Retoucher PRO (HQ) - 16Mpx"` (4000×4000, native). HQ tiers are more precise and detail-faithful.
- `aspect_ratio` (string, default `"auto"`): `"auto"`, `"1:1"`, `"1:4"`, `"1:8"`, `"2:3"`, `"3:2"`, `"3:4"`, `"4:1"`, `"4:3"`, `"4:5"`, `"5:4"`, `"8:1"`, `"9:16"`, `"16:9"`, `"21:9"`. With an extreme-ratio input, never leave `"auto"` — pick the value matching the source to avoid unexpected cropping.
- `bg_mode` (string, default `"off"`): `"off"` | `"solid"` | `"transparent"`. Solid/transparent is post-processing (+3 credits); `bg_color` (string, default `"#ffffff"`) sets the solid color; `bg_shadow` (boolean, default `true`) adds a natural contact shadow on solid backgrounds (free).
- `framing_enabled` (boolean, default `false`) + `framing_pad_top` / `framing_pad_right` / `framing_pad_bottom` / `framing_pad_left` (numbers 0–100, default 15): crops to the item and re-frames with per-side padding as a percent of the item's longest side; output keeps the selected `aspect_ratio`, item never scaled. Free.
- `retouch_gemstones` (boolean, default `true`): `true` retouches the stone surfaces carefully (dust, lint, smudges, fine scratches) and keeps natural inclusions; `false` leaves the stones exactly as photographed (metal and background are still retouched).
- `vintage_jewelry` (boolean, default `false`, free): `true` is for vintage and antique pieces -- the metal is cleaned (dust, lint, fingerprints) but keeps its own finish, texture, color and soft sheen, including the darker shading in engravings and recesses, with no high polish or added shine; `false` polishes the metal to the bright, high-shine catalog finish. Works on every tier.
- `debanding` (boolean, default `false`, +1 credit): removes wave-like patterns on polished metal and smooth gradients; may soften textured surfaces or gemstone detail, so the run returns two versions (original + de-banded) to compare.
- `color` (string, default `"Not Selected"`): up to 3 reference colors (metal, gemstone, enamel, ...). Picks only take effect when `user_instructions` names them as "Color #1", "Color #2", "Color #3".
- `user_instructions` (string, default `""`): **leave blank** except to map custom colors or protect a specific marking. Never enter general retouching instructions — they cause overexposed metal, excess sparkle, and unnatural shine, especially at HQ tiers.
- Output: `extension` `"png"` | `"jpg"` | `"webp"` (default `"png"`); `quality` (integer 50–100, default 80, JPEG); `dpi` (integer 72–300, default 300).

**Recipe — standard e-commerce shot on white with shadow (23 credits):**
```json
{"input_image_1": "https://example.com/ring-tight-crop.jpg", "bg_mode": "solid", "bg_color": "#ffffff", "bg_shadow": true, "aspect_ratio": "1:1"}
```

**Recipe — 4 Mpx marketplace hero, transparent PNG for compositing (43 credits):**
```json
{"input_image_1": "https://example.com/necklace.jpg", "model_tier": "Retoucher PRO (HQ) - 4Mpx", "bg_mode": "transparent", "extension": "png", "aspect_ratio": "4:5"}
```

**Recipe — maximum-fidelity 16 Mpx print/zoom asset, de-banded polished metal (81 credits):**
```json
{"input_image_1": "https://example.com/bracelet-4000px.jpg", "model_tier": "Retoucher PRO (HQ) - 16Mpx", "debanding": true, "aspect_ratio": "1:1", "extension": "png"}
```

**Recipe — honest listing of a natural stone, framed with tighter padding (20 credits):**
```json
{"input_image_1": "https://example.com/emerald-ring.jpg", "retouch_gemstones": false, "framing_enabled": true, "framing_pad_top": 10, "framing_pad_right": 10, "framing_pad_bottom": 10, "framing_pad_left": 10, "aspect_ratio": "4:5"}
```

## Common failures -> cause -> fix

- **Scratches, tarnish, oxidation, dirt still visible after the run** -> by design: Precision refines the piece as photographed and never repairs physical wear -> do not re-run here; switch to **Jewelry Retoucher Rescue** with the same image. (Confirmed support pattern: rough or oxidized pieces sent to Precision always come back unfixed.)
- **Piece position or orientation changed in the output** -> too much empty background around a small subject gives the app room to re-stage it -> crop tightly to the piece and pass the cropped URL; fix rotation in the file before hosting.
- **Result too soft / lacks detail at HQ tier** -> Precision preserves the source; it does not invent detail from a soft or low-res input -> provide a sharper, larger source (≥2000 px), or use **Jewelry Retoucher Rescue** which reconstructs.
- **Metal color wrong (source photo dark or color-cast)** -> color is read from the pixels; dark/tinted sources mislead it -> supply a brighter, neutrally lit source, or set a reference color and name it ("Color #1 is the metal") in `user_instructions`; for very dark photos prefer **Jewelry Retoucher Rescue**.
- **Vintage piece came back too polished, too bright, or lost its satin/brushed/engraved texture** -> the default polishes metal to a high-shine catalog finish -> resubmit with `"vintage_jewelry": true` (and no general retouching instructions).
- **Overexposed metal, excessive sparkle, unnatural shine** -> `user_instructions` duplicated what the app already does automatically -> resubmit with `user_instructions` empty.
- **Gemstone looks glassy, over-clean, or lost inclusions** -> the result is generated; `retouch_gemstones: true` (the default) cleans only the stone surfaces and keeps inclusions -> set `"retouch_gemstones": false` to leave the stones exactly as photographed; if facets vanished, the cause is source glare/softness — use a straight-on, glare-free, sharp source with the stone larger in frame.
- **Engraved text, hallmark, or karat stamp altered** -> the marking was small, soft, or angled in the source, so it was rebuilt as generic surface detail -> provide a sharp, straight-on, glare-free close-up of the marking; add one instruction naming it ("keep the 14K hallmark exactly where it is, do not restyle it"). Never publish a result with an altered karat/maker mark — it misrepresents the item.
- **Specks rendered as extra tiny stones** -> dust/debris in channels reads as set stones -> clean the piece physically and reshoot; verify the output stone count before publishing.
- **Output cropped unexpectedly** -> extreme input ratio with `aspect_ratio: "auto"` -> set an explicit enum value matching the input.
- **Result shows the same missing edge as the source** -> part of the piece was cut off in the photo -> provide a source with the full piece in frame.
- **Input is an on-model/worn photo** -> unsupported; it cannot extract the piece -> provide a product shot on a plain surface, or route the request to the matching try-on app.

## Cost notes

- **Base: 20 credits** per run (at default `model_tier` `"Retoucher SD - 1Mpx"`). Regeneration of a run: 7 credits base.
- `model_tier` `"Retoucher PRO (HQ) - 4Mpx"`: **+20** (run = 40; regeneration +8).
- `model_tier` `"Retoucher PRO (HQ) - 16Mpx"`: **+60** (run = 80; regeneration +19).
- `bg_mode` `"solid"` or `"transparent"`: **+3** (also +3 on regeneration). `bg_shadow` and framing controls are free.
- `debanding: true`: **+1** (also +1 on regeneration).
- Each run returns one result and is charged once; to compare several results, submit several runs, each charged separately (e.g., 2 runs at 4 Mpx = 80).
- Charge = base + every matching credit rule; call `estimate_run_cost` for the exact figure before submitting when cost matters.

Worked examples (single run):

| Configuration | Run | Regeneration |
|---|---|---|
| Defaults (`"Retoucher SD - 1Mpx"`, `bg_mode: "off"`) | 20 | 7 |
| SD + `bg_mode: "solid"` | 23 | 10 |
| `"Retoucher PRO (HQ) - 4Mpx"` + `bg_mode: "transparent"` | 43 | 18 |
| `"Retoucher PRO (HQ) - 16Mpx"` + `debanding: true` | 81 | 27 |

Spending strategy: prototype framing, background, and color mapping at `"Retoucher SD - 1Mpx"` (20/run), then re-run the winning parameter set once at an HQ tier — HQ runs cost 2–4x, so never iterate at 16 Mpx.
