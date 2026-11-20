# Jewelry Retoucher Vivid — Agent Guide

_Reach for this app when a good jewelry photo needs to become a bold, saturated marketing asset — richer color, deeper shadows, stronger contrast than a neutral catalog look._

## What it does — and what it will NOT do

**Does:**
- Retouches a single jewelry product photo into a rich, high-impact rendering with deeper shadows, stronger contrast, and more color intensity than a neutral catalog treatment. This is a deliberate look for marketing, social, and editorial use — not a lower-quality tier.
- Retouches the stone surfaces by default (`retouch_gemstones`: dust, lint, smudges and fine scratches go, natural inclusions stay), and lets you switch that off to leave the stones exactly as photographed.
- Accepts up to 3 reference colors (`color`) that you name in `user_instructions` ("Color #1", "Color #2"…) so specific metal, stone, or enamel tones render accurately.
- Optional post-processing: place the piece on a solid-color background with a natural contact shadow, or output a fully transparent background; precise re-framing with per-side padding.
- One result per run, output as PNG, JPG, or WebP with DPI and JPEG-quality control.

**Will NOT do:**
- Repair physical wear — scratches, dents, tarnish, oxidation, or dirt on the piece itself.
- Recover blurry, dark, or otherwise poor photos. It enhances real detail; it cannot invent what the source does not show.
- Output above about 16 megapixels (4K sizes top out there; HQ is about 4 Mpx, SD about 1 Mpx).
- Place jewelry on a model, build styled scenes, or change the design (swap metals/stones, resize, add or remove elements).

## Choose it / don't choose it

| Situation | Verdict |
|---|---|
| Instagram/Pinterest creative, ad hero image, editorial shot with mood | **Choose it** |
| Need a transparent PNG or solid-color background straight out of the run | **Choose it** |
| Listing where the stones must stay exactly as shot | **Choose it**, set `retouch_gemstones: false` |
| Neutral, faithful catalog look | Use **Jewelry Retoucher Precision** (faithful, up to 16 Mpx) |
| Blurry, dark, low-quality source, or a worn/scratched/tarnished piece | Use **Jewelry Retoucher Rescue** |
| Styled scenes and creative backgrounds in the same rich look | Use **Creative Jewelry Photographer Vivid** |
| Change metal, stones, finish, or design | Use **Jewelry Modifier Pro** |
| Show the piece on a person | Use **Rings Try On** / **Earrings Try On** / **Necklace Try - On** / **Bracelet Try-ON** / **Watch Try-On** |

## Inputs that make or break the result

- `input_image_1` is the only image input: a publicly reachable HTTPS URL to the jewelry photo.
- **Fully in focus, details clearly visible, minimum ~2000px** on the shorter side. Soft or low-resolution sources are the #1 cause of fake/plastic-looking output and changed details.
- **Crop before you host.** If the piece is small in the frame with lots of empty background, crop tight to the subject first and pass the cropped version's URL. Small-in-frame subjects cause reconstructed (wrong) details.
- **Even, neutral lighting on a neutral background.** Colored walls, cloths, or lamps reflect into metal and shift its tone; glare on stone tables washes out facets.
- **Physically clean the piece before shooting.** Dust, polishing residue, or debris in channels and prong seats can be rendered as tiny stones instead of removed.
- Do NOT pre-cut the background — background handling is done by the app; pass the photo as shot (after cropping/rotating).
- Rotate to the correct orientation before hosting if the piece is sideways.

**Pre-flight checklist before spending credits:**
1. One sharp, in-focus product photo, ≥2000px, piece filling the frame (crop before hosting if not).
2. Neutral lighting, neutral background, no glare across stone tables, piece physically clean.
3. `retouch_gemstones` matches intent: `true` (default) for cleaned stone surfaces with natural inclusions kept, `false` to leave the stones exactly as photographed.
4. If exact tones matter: `color` set AND `user_instructions` maps each "Color #N" to its element; otherwise `user_instructions` empty.
5. Transparency requested -> `bg_mode: "transparent"` with `extension` `"png"` or `"webp"`, never `"jpg"`.

## Parameter playbook

Exact names and values from the schema. Omitted optionals use their defaults.

| Parameter | Type | Default | Values / range |
|---|---|---|---|
| `input_image_1` | image_url | — | required; HTTPS URL |
| `retouch_gemstones` | boolean | `true` | `true` cleans the stone surfaces and keeps inclusions, `false` leaves the stones exactly as photographed |
| `color` | string | `"Not Selected"` | up to 3 reference colors; name them in `user_instructions` as "Color #1"… |
| `user_instructions` | string | empty | ONLY for mapping custom colors to elements; otherwise leave blank |
| `image_size` | string | `"Square SD"` | `"Auto SD"`, `"Square SD"`, `"Portrait 4:3 SD"`, `"Portrait 16:9 SD"`, `"Landscape 4:3 SD"`, `"Landscape 16:9 SD"`, `"Auto HQ"`, `"Square HQ"`, `"Portrait 4:3 HQ"`, `"Portrait 16:9 HQ"`, `"Landscape 4:3 HQ"`, `"Landscape 16:9 HQ"`, `"Auto 4K"`, `"Square 4K"`, `"Portrait 4:3 4K"`, `"Portrait 16:9 4K"`, `"Landscape 4:3 4K"`, `"Landscape 16:9 4K"` |
| `bg_mode` | string | `"off"` | `"off"`, `"solid"`, `"transparent"` (+3 credits when not off) |
| `bg_color` | string | `"#ffffff"` | hex color, used with `bg_mode: "solid"` |
| `bg_shadow` | boolean | `true` | soft contact shadow on solid background (free) |
| `framing_enabled` | boolean | `false` | crop + re-frame with per-side padding (free) |
| `framing_pad_top` / `framing_pad_right` / `framing_pad_bottom` / `framing_pad_left` | number | `15` | 0–100, percent of the item's longest side; item is never scaled |
| `extension` | string | `"png"` | `"png"`, `"jpg"`, `"webp"` |
| `lossless_webp` | string | `"true"` | `"false"`, `"true"` (WebP only) |
| `dpi` | integer | `300` | 72–300 |
| `quality` | integer | `80` | 50–100 (JPEG only) |

SD sizes render about 1 Mpx, HQ about 4 Mpx, 4K about 16 Mpx (a 4K run takes 2 to 5 minutes). `"Auto SD"` / `"Auto HQ"` / `"Auto 4K"` match the input's aspect ratio.

**Recipe 1 — social/ad hero, matching input aspect, high res:**
```json
{"input_image_1": "https://example.com/ring-2400px.jpg", "image_size": "Auto HQ"}
```

**Recipe 2 — honest listing shot, natural stone preserved:**
```json
{"input_image_1": "https://example.com/emerald-pendant.jpg", "retouch_gemstones": false, "image_size": "Square HQ"}
```

**Recipe 3 — exact metal tone enforced via reference color:**
```json
{"input_image_1": "https://example.com/band.jpg", "color": "#e8c39e", "user_instructions": "The band metal is Color #1.", "image_size": "Auto HQ"}
```
Color picks only take effect when `user_instructions` names them ("Color #1", "Color #2", "Color #3"). If a background color is also selected there is no need to mention the background in the instructions.

**Recipe 4 — transparent cut-out asset for compositing:**
```json
{"input_image_1": "https://example.com/necklace.jpg", "bg_mode": "transparent", "extension": "png", "image_size": "Square HQ"}
```

**Recipe 5 — packshot on brand color with shadow and tight framing:**
```json
{"input_image_1": "https://example.com/ring.jpg", "bg_mode": "solid", "bg_color": "#f5f0e8", "bg_shadow": true, "framing_enabled": true, "framing_pad_top": 10, "framing_pad_right": 10, "framing_pad_bottom": 12, "framing_pad_left": 10, "image_size": "Square HQ"}
```

## Common failures -> cause -> fix

- **Jewelry details came back changed / "completely different piece"** -> the subject was too small in the frame, so unresolvable detail was reconstructed -> crop tight to the piece before hosting and pass the cropped URL; use the sharpest source available (≥2000px).
- **Metal or stone color is wrong** -> colored ambient light, colored background reflections, glare, or too few pixels on the piece -> reshoot/choose a neutrally lit photo on a plain background, crop closer, and set the tone explicitly via `color` + `user_instructions` ("the band is Color #1") instead of relying on the photo.
- **Gemstones look too clean / lost their real character** -> the result is generated; `retouch_gemstones: true` (the default) cleans the stone surfaces and keeps inclusions -> re-run with `retouch_gemstones: false` to leave the stones exactly as photographed.
- **Stone looks glassy or lost its cut** -> facets were not visible in the source (soft focus, glare across the table, underexposure, stone too small in frame) -> straight-on shot, diffused light, sharp focus, tighter crop. If the source is dark overall, brighten it before hosting or switch to **Jewelry Retoucher Rescue**.
- **Result looks fake, plastic, or over-processed** -> low-resolution or soft source gives the app too little real texture to enhance -> supply a sharper, larger photo (≥2000px, piece filling the frame, even diffused light). This one change fixes most cases.
- **Sparkle or extra "stones" appeared that aren't on the piece** -> dust, polishing compound, or debris in channels/prongs read as tiny stones -> physically clean the piece and reshoot; a higher-resolution source also disambiguates specks from stones. Never publish a result showing stones the item does not have.
- **Engraving, hallmark, or karat stamp altered** -> the marking was too small, soft, or angled to resolve, so it was rebuilt -> provide a sharp, straight-on, glare-free close-up of the marking, or frame it out of the shot. For markings that must survive exactly, **Jewelry Retoucher Precision** is the faithful option. Never use an image with an altered hallmark or karat mark commercially.
- **Scratches, tarnish, or dirt still visible** -> this app polishes good photos, it does not repair physical wear -> route to **Jewelry Retoucher Rescue**.
- **Output too saturated / too contrasty** -> that is this app's intended character -> for a natural, true-to-the-piece rendering use **Jewelry Retoucher Precision**.
- **Fine detail soft, or a larger file is needed** -> pick a 4K `image_size` (about 16 Mpx) with a sharp source photo; for a neutral, faithful rendering use **Jewelry Retoucher Precision**.
- **Result looks overdone after free-text instructions** -> `user_instructions` here is only for mapping custom colors, not general retouch direction -> resend with `user_instructions` empty unless `color` is set.
- **Transparent background came back flattened** -> JPEG cannot hold transparency -> use `bg_mode: "transparent"` with `extension: "png"` or `"webp"`.

## Cost notes

- **Base: 25 credits** per run; **regeneration: 9 credits**.
- Any HQ `image_size` value (`"Auto HQ"`, `"Square HQ"`, `"Portrait 4:3 HQ"`, `"Portrait 16:9 HQ"`, `"Landscape 4:3 HQ"`, `"Landscape 16:9 HQ"`): **+15 credits** (+9 on regeneration). SD sizes add nothing.
- Any 4K `image_size` value (`"Auto 4K"`, `"Square 4K"`, `"Portrait 4:3 4K"`, `"Portrait 16:9 4K"`, `"Landscape 4:3 4K"`, `"Landscape 16:9 4K"`): **+75 credits** (+60 on regeneration).
- `bg_mode` `"solid"` or `"transparent"`: **+3 credits** (+3 on regeneration). `bg_shadow` and framing controls are free.
- Each run returns one result and is charged once; to compare several results, submit several runs, each charged separately.
- Examples: SD run = 25; HQ run = 40; HQ + solid background = 43; HQ + transparent, 2 separate runs = 86; 4K run = 100.
- Use `estimate_run_cost` for the exact number before committing a run.
