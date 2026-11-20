# Jewelry Retoucher Rescue — Agent Guide

_Reach for this app when the photo is rough (blurry, dark, noisy, low-res, glary) OR the piece itself is worn, scratched, tarnished, or dirty — it recovers both into a clean product image._

## What it does — and what it will NOT do

**Does:** Heavy-duty recovery retouching. It reconstructs and cleans aggressively: bad lighting, blur, harsh glare, noise, low resolution, phone photos, screenshots, and cluttered backgrounds on the photo side; scratches, dents, tarnish, oxidation, dirt, and fingerprints on the piece itself — regardless of how rough either is. It honors plain-language `user_instructions` ("replace the background with deep navy", "remove the glare on the diamond"). It runs on one of two models, chosen with `render_model`: **PRO** (the API default) renders fixed pixel sizes up to 3840×2160 / 2880×2880 at five quality levels and supports **area masking** (pass `{"url": ..., "mask_url": ...}` for `jewelry_photo` to retouch only the masked region and leave the rest untouched); **STANDARD** (the web app's default, shown there as "SD") renders 1K or 2K in any common aspect ratio, including "auto" to keep the photo's own shape, at low or medium quality, with no masking and no de-banding. Accepts all jewelry types including watches and timepieces. Outputs a clean product image on a white, transparent, or custom solid background.

**Will NOT do:**
- Guarantee exact preservation of fine detail on very rough sources — reconstruction interprets what it cannot see clearly; that is the trade that lets it clean up damage.
- Extract jewelry from a photo where a person is wearing it, place jewelry on a model, build styled scenes, or make structural design changes (add/remove stones, resize, swap stone types).
- Fix in one pass every case of extreme damage or extreme photo quality — severe sources may need a clearer angle plus targeted instructions.

## Choose it / don't choose it

Choose Rescue when **either** is true: the photo is poor, OR the piece has visible wear. If both photo and piece are pristine, it is the wrong tool.

| Don't choose Rescue for... | Use instead |
|---|---|
| Sharp, high-res photo of a clean, flawless piece needing maximum faithful detail | **Jewelry Retoucher Precision** — preserves rather than reconstructs, up to 16 Mpx |
| Targeted tweaks to an already-retouched, rendered, or edited image | **Jewelry Modifier Pro** — it changes only the named elements; a full Rescue re-process makes smooth renders look artificial or "painted". Never tell the user to reshoot or downgrade to an unedited photo in this case. |
| Bold, saturated marketing look from an already-clean photo | **Jewelry Retoucher Vivid** |
| Jewelry on a model / try-on | **Rings Try On**, **Earrings Try On**, **Necklace Try - On**, **Bracelet Try-ON**, or **Watch Try-On** |
| Creative scenes or styled backgrounds | **Creative Jewelry Photographer Vivid** |
| Structural design changes (stone type swap, add/remove elements, resize) | **Jewelry Modifier Pro** (COLOR changes are fine here — describe them in `user_instructions`) |
| Fine manual control over processing strength | **AI Photo Editor Pro – Retouching & Editing V2** |
| Loose (unset) gemstones, or a job needing 4–5 reference colors | **Jewelry Retoucher Rescue (Legacy)** — the only retoucher with a `loose_gemstones` mode and 5 color slots |

Decision quick-check: rough photo OR rough piece -> Rescue. Sharp photo AND clean piece -> **Jewelry Retoucher Precision**. Finished render needing a tweak -> **Jewelry Modifier Pro**.

## Inputs that make or break the result

- `jewelry_photo` is a publicly reachable HTTPS URL (or `{"url", "mask_url"}` object for masked runs). **Minimum ~2000 px on the shortest side recommended** — even for a "rescue", more true pixels means less invented detail. Tiny/low-res sources are the top cause of wrong stone shapes and invented geometry.
- **Supported input aspect ratios: 2:1 through 1:2.** Crop anything more extreme before hosting.
- **Crop tight to a single piece.** Loose framing around a small subject is the confirmed root cause of three failure classes here: fine details changed, metal color drifting (e.g., everything turning white-gold), and wrong orientation. One piece per run; crop or split multi-piece photos.
- **Rotate before hosting** so the piece is right-side up; output mirrors source orientation.
- **Full piece in frame** — an edge-cropped source yields an edge-cropped result.
- Give the **clearest angle you have** of a rough piece, and name any detail that must survive in `user_instructions` (e.g., "keep the milgrain edge on the band exactly as it is").
- PRO: set `aspect_ratio` to a size matching the input's orientation (portrait source -> portrait size) to avoid the subject being cropped. Quick mapping: square input -> `"1024x1024"`/`"2048x2048"`/`"2880x2880"`; portrait -> `"1024x1536"`/`"1440x2560"`/`"2160x3840"`; landscape -> `"1536x1024"`/`"1920x1080"`/`"2560x1440"`/`"3840x2160"`. STANDARD: send `"auto"` to keep the input's shape, or a ratio.
- For masked runs, the mask image must also be a publicly reachable HTTPS URL, aligned to the source image's dimensions, covering exactly the region to retouch.
- Product shots only — never an on-model/lifestyle photo.

## Parameter playbook

Exact names and enum values (copy enum strings exactly; off-spec values are rejected):

- `jewelry_photo` (image_url, **required**) — HTTPS URL string, or `{"url": "...", "mask_url": "..."}` to restrict retouching to the masked area only (PRO only; a mask with STANDARD is refused before any charge).
- `render_model` (string, default `"PRO"`): `"PRO"` | `"STANDARD"`. Omit it to keep PRO, exactly as before this option existed.
- `aspect_ratio` (string, default `"1024x1024"`):
  - PRO: an output pixel size — `"1024x1024"`, `"1024x1536"`, `"1536x1024"`, `"1920x1080"`, `"2048x2048"`, `"1440x2560"`, `"2560x1440"`, `"2880x2880"`, `"2160x3840"`, `"3840x2160"`; match the input's orientation to avoid cropping the subject. A ratio with PRO is refused.
  - STANDARD: `"auto"` (keep the input's shape; the default when STANDARD is sent without it) or a ratio — `"1:1"`, `"3:4"`, `"4:3"`, `"16:9"`, `"9:16"`, `"2:3"`, `"3:2"`, `"2:1"`, `"1:2"`, `"20:9"`, `"9:20"`, `"19.5:9"`, `"9:19.5"`. A pixel size sent with STANDARD becomes its nearest ratio.
- `resolution` (string, STANDARD only, default `"1K"`): `"1K"` (about 1 megapixel) | `"2K"` (about 4 megapixels). PRO ignores it.
- `render_image_quality` (string): PRO `"low"` | `"medium"` | `"high"` | `"xhigh"` | `"max"` (default `"high"`); STANDARD `"low"` | `"medium"` (default `"low"` when STANDARD is sent without it; higher values render at medium). Main quality/price lever (see Cost notes).
- `user_instructions` (string, default `""`, any language): short targeted directives — color mapping ("Color #1 is the prongs"), specific fixes ("remove the glare on the center stone"), background requests ("deep navy background"), or details to protect. Keep it specific; do not stack generic "retouch it nicely" text.
- `color` (string, default `"Not Selected"`): up to 3 reference colors for metal/gemstone/enamel. They only take effect when `user_instructions` references them as "Color #1", "Color #2", "Color #3".
- `bg_mode` (string, default `"off"`): `"off"` | `"solid"` | `"transparent"` (+3 credits for solid/transparent); `bg_color` (string, default `"#ffffff"`); `bg_shadow` (boolean, default `true`, free) adds a natural contact shadow on solid backgrounds.
- `framing_enabled` (boolean, default `false`) + `framing_pad_top` / `framing_pad_right` / `framing_pad_bottom` / `framing_pad_left` (numbers 0–100, default 15): crop-and-reframe with per-side padding as percent of the item's longest side; keeps the selected `aspect_ratio`, never scales the item. Free.
- `retouch_gemstones` (boolean, default `true`): `true` retouches the stone surfaces carefully (dust, lint, smudges, fine scratches) and keeps natural inclusions; `false` leaves the stones exactly as photographed (metal and background are still retouched).
- `debanding` (boolean, default `false`, +1 credit, PRO only — STANDARD results do not need it and it is switched off): kills wave-like patterns on polished metal/gradients; may soften texture or stone detail, so you receive both original and de-banded versions.
- Output: `extension` `"png"` | `"jpg"` | `"webp"` (default `"png"`); `quality` (integer 50–100, default 70, JPEG); `lossless_webp` `"false"` | `"true"` (default `"true"`, webp only); `dpi` (integer 72–300, default 300).

**Recipe — cheap triage pass to see what is recoverable (5 credits):**
```json
{"jewelry_photo": "https://example.com/estate-ring-phone-shot.jpg", "render_image_quality": "low", "aspect_ratio": "1024x1024"}
```

**Recipe — STANDARD rescue that keeps the photo's own shape (15 credits):**
```json
{"jewelry_photo": "https://example.com/dim-ring-phone-shot.jpg", "render_model": "STANDARD", "resolution": "1K", "render_image_quality": "low", "aspect_ratio": "auto"}
```

**Recipe — standard rescue of a worn piece, portrait listing, white background (13 credits):**
```json
{"jewelry_photo": "https://example.com/tarnished-pendant-tight-crop.jpg", "render_image_quality": "medium", "aspect_ratio": "1024x1536", "bg_mode": "solid", "bg_color": "#ffffff", "bg_shadow": true, "user_instructions": "clean up the tarnish on the chain, keep the engraved pattern on the pendant unchanged"}
```

**Recipe — masked spot-fix that leaves the rest of an image untouched (10 credits, PRO):**
```json
{"jewelry_photo": {"url": "https://example.com/ring.jpg", "mask_url": "https://example.com/ring-stone-mask.png"}, "render_image_quality": "medium", "aspect_ratio": "1024x1024", "user_instructions": "remove the glare on the center stone"}
```

**Recipe — high-end 2048×2048 recovery with custom backdrop (43 credits):**
```json
{"jewelry_photo": "https://example.com/vintage-brooch.jpg", "render_image_quality": "high", "aspect_ratio": "2048x2048", "bg_mode": "solid", "bg_color": "#0b1f3a", "retouch_gemstones": false}
```

## Common failures -> cause -> fix

- **Fine detail changed (stone count, shapes, small elements)** -> reconstruction interprets what the source does not show clearly; loose framing and low resolution amplify it -> crop tightly, provide ≥2000 px on the shortest side, and name the critical details to keep in `user_instructions`; if the source is actually sharp and the piece clean, run **Jewelry Retoucher Precision** instead.
- **Metal color came out wrong (e.g., yellow gold turned white)** -> piece too small in frame plus colored light/background, so color was read from too few, tinted pixels -> crop closer; reshoot in neutral light; or set a reference color and map it ("Color #1 is the band, yellow gold").
- **Result reads as fake / plastic / "false jewellery"** -> very low-res or soft source left nothing real to enhance, so surfaces were rebuilt smooth -> supply a larger, sharper source (≥2000 px shortest side) with the piece filling the frame; do not pre-cut the background — background handling is automatic.
- **Wrong stone shapes reported (e.g., pears/marquise rendered as ovals)** -> source too small and low-quality for the cuts to read -> provide a higher-resolution source above ~2000×2000 and re-run.
- **Orientation wrong in output** -> rotated source and/or excess empty background -> rotate the file upright and crop close before hosting.
- **Piece still not clean enough** -> severe damage or extreme photo limits single-pass recovery -> provide a clearer/better-lit source of the same piece and describe the residual problem ("remove scratches on the band", "clean up the tarnish").
- **Gemstone too smooth / lost inclusions** -> the result is generated; `retouch_gemstones: true` (the default) cleans only the stone surfaces and keeps inclusions -> set it `false` to leave the stones exactly as photographed.
- **Specks rendered as extra tiny stones** -> dirt/debris in channels reads as set stones -> physically clean the piece before shooting; or state "the marks along the side rail are dirt, not stones — remove them"; verify stone count before publishing.
- **Engraving/hallmark redrawn or moved** -> marking not sharp/legible in source, so it was rebuilt -> provide a straight-on, glare-free close-up and an explicit keep-instruction; for hallmark-critical work use **Jewelry Retoucher Precision** with a sharp photo of a clean piece. Never publish a result with an altered karat or maker's mark.
- **Subject cropped in output** -> `aspect_ratio` orientation does not match the input -> pick the enum size matching the source's orientation (or use `framing_enabled` with padding).
- **An already-retouched render came back looking painted** -> Rescue re-processes the whole image from scratch -> route targeted edits on finished images to **Jewelry Modifier Pro**.
- **On-model photo submitted** -> unsupported; it cannot extract the worn piece -> provide a product shot on a plain surface, or use the matching try-on app.

## Cost notes

- **Base: 5 credits**, plus an additional charge that depends on the model.
- PRO: the `aspect_ratio` × `render_image_quality` matrix (total per run):

| `aspect_ratio` | `"low"` | `"medium"` | `"high"` | `"xhigh"` | `"max"` |
|---|---|---|---|---|---|
| `1024x1024`, `1024x1536`, `1536x1024`, `1920x1080` | 5 | 10 | 20 | 25 | 35 |
| `2048x2048`, `1440x2560`, `2560x1440` | 30 | 35 | 40 | 50 | 60 |
| `2880x2880` | 40 | 45 | 60 | 70 | 85 |
| `2160x3840`, `3840x2160` | 45 | 50 | 60 | 70 | 85 |

- STANDARD: `resolution` × `render_image_quality` (total per run), any `aspect_ratio`:

| `resolution` | `"low"` | `"medium"` |
|---|---|---|
| `1K` | 15 | 25 |
| `2K` | 25 | 35 |

- `bg_mode` `"solid"` or `"transparent"`: **+3**. `debanding: true`: **+1**. Shadow and framing are free.
- Each run returns one result and is charged once; to compare several looks, submit several runs, each charged separately.
- Regeneration costs less than a first run: PRO `1024x1024` `"high"` regenerates for 7 and `2048x2048` `"high"` for 14; STANDARD 1K low / 1K medium / 2K low / 2K medium for 7 / 10 / 10 / 12; `bg_mode` adds 3 and `debanding` 1.
- The charge is base + every matching rule; call `estimate_run_cost` for the exact number before costly high-res runs.

Spending strategy: run PRO `"low"` at a 1024-class size first (5 credits) to check whether the source is recoverable and the framing/instructions are right, then re-run the winning parameter set once at the target model, size and quality. A `3840x2160` `"high"` run costs 12x the triage pass — never iterate there. This is also the cheapest retoucher to experiment with overall: its 5-credit floor undercuts both **Jewelry Retoucher Precision** (20) and **Jewelry Retoucher Rescue (Legacy)** (25).
