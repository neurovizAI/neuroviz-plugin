# Watch Try-On — Agent Guide

_Reach for this app when a product photo of a complete wrist watch (dress, sport, luxury, or smart) needs to appear on a model's wrist as a studio-grade image._

## What it does — and what it will NOT do

**Does:**
- Places the watch from a product photo onto a model's wrist, keeping the watch exactly as it appears in the source photo.
- The model can come from an optional reference image URL or be generated automatically (steered by `gender` and `user_instructions`).
- Renders either a tight wrist close-up (best for dial detail — the default) or a wider shot of the model, on a clean studio-white background or in a described lifestyle scene.
- Scales the watch on the wrist via a size selector (`XS` to `XL`).
- Outputs an image at three quality tiers up to 16Mpx native (~4000×4000), with one result per run and full output control (aspect ratio, format, JPEG quality, DPI).

**Will NOT do:**
- Retouch, polish, or enhance the watch — it is placed as-is, flaws included.
- Change the design: dial, strap, case, or markings stay whatever the photo shows.
- Preserve the model reference exactly. The reference is a STYLE reference: the result looks like the same person, but the photo is regenerated — pose, wrist shape, and fine details will differ.
- Handle bracelets, fitness bands without a time display, watch parts, or other jewelry types.
- Follow placement commands ("put the watch on her wrist") — placement is automatic; such instructions only confuse the result.

## Choose it / don't choose it

| Situation | Verdict |
|---|---|
| Complete assembled wrist watch, product photo on a plain background | **Choose it** |
| E-commerce listing, catalog, or lookbook context shots; comparing model styles or scenes on one watch | **Choose it** — use `is_lifestyle`, and submit several runs (each charged separately) to compare looks |
| Bracelet, bangle, or fitness band with no visible watch face / time display | Don't — **Bracelet Try-ON** |
| Ring | Don't — **Rings Try On** |
| Earrings | Don't — **Earrings Try On** |
| Necklace or pendant | Don't — **Necklace Try - On** (a pocket watch on a chain also often works better there, with placement described in instructions) |
| Several jewelry types on one model in one go | Don't — **Jewelry Set Try-On** (note: full-set try-on doesn't include watches — run the watch separately here) |
| Watch parts alone (case, strap, dial) or a watch needing cleanup/polish | Don't — **Jewelry Retoucher Precision** (clean, sharp photo) or **Jewelry Retoucher Rescue** (rough or worn one) first, then return here |
| Change the dial, strap, or case design | Don't — **Jewelry Modifier Pro** first, then try on the modified result |

## Inputs that make or break the result

- `input_image_1` (the watch, required): a clear product photo of the **complete assembled watch**, ideally **2000px or more on the smallest side**, on a plain background, passed as a publicly reachable HTTPS URL (plain string).
- **Crop before hosting.** If the watch is small in the frame with lots of empty background, fine dial detail gets lost — crop tight around the watch and pass the cropped version's URL. Rotate a sideways image before hosting too; pre-run fixes beat re-generating.
- **One watch per image.** With multiple watches visible, either crop to the one you want or disambiguate in `user_instructions` (e.g. "use the gold one").
- A worn-on-wrist source photo sometimes works (the app tries to extract the watch) but results vary — a clean product shot is far more reliable.
- Show the whole piece straight-on without extreme perspective so its proportions are read correctly; pair with `product_size` for scale.
- `input_image_2` (the model, optional): a single person, ideally **no other jewelry and a bare placement wrist** — an existing watch, bracelets, or a sleeve fully covering the wrist all interfere with placement; some wrist skin must be visible. When in doubt, omit it and let the model be generated.
- Match the toggles to the reference: a wrist close-up reference wants `is_closeup: true`; a wide or lifestyle reference wants `is_closeup: false` and/or `is_lifestyle: true` — otherwise the output style won't match the reference's framing.
- Clean the watch before photographing: dust or debris in the source can be misread as detail and rendered into the result.

## Parameter playbook

Exact schema names and values (base cost 20 credits; enum strings must match case-exactly, including the parenthetical parts; omitted optional parameters use their defaults):

| Parameter | Type | Values / range | Default | Notes |
|---|---|---|---|---|
| `input_image_1` | image_url | HTTPS URL string | — | **Required.** The watch; ≥2000px smallest side preferred |
| `product_size` | string | `"XS (Extra Small)"`, `"S (Small)"`, `"M (Medium)"`, `"L (Large)"`, `"XL (Extra Large)"` | `"M (Medium)"` | Scale of the watch on the wrist |
| `user_instructions` | string | free text, multiline, any language | `""` | Model/scene preferences ONLY — never placement commands. Blank is best |
| `input_image_2` | image_url | HTTPS URL string | `""` | Optional model reference (style reference, not preserved exactly); omit to auto-generate |
| `is_lifestyle` | boolean | `true`/`false` | `true` | `true` = real-world scene (describable in `user_instructions`); `false` = studio white |
| `is_closeup` | boolean | `true`/`false` | `true` | `true` = tight crop around hand/wrist (best dial detail); `false` = more of the model |
| `gender` | string | `"female"`, `"male"` | `"female"` | Model's gender |
| `model_tier` | string | `"ViTryon SD - 1Mpx"`, `"ViTryon PRO (HQ) - 4Mpx"`, `"ViTryon PRO (HQ) - 16Mpx"` | `"ViTryon SD - 1Mpx"` | ~1024², ~2000², ~4000² native output; PRO tiers are more precise and cost more |
| `aspect_ratio` | string | `"auto"`, `"1:1"`, `"1:4"`, `"1:8"`, `"2:3"`, `"3:2"`, `"3:4"`, `"4:1"`, `"4:3"`, `"4:5"`, `"5:4"`, `"8:1"`, `"9:16"`, `"16:9"`, `"21:9"` | `"auto"` | `auto` matches the input crop |
| `extension` | string | `"png"`, `"jpg"`, `"webp"` | `"png"` | Output format |
| `quality` | integer | 50–100 | `80` | JPEG quality (applies with `"jpg"`) |
| `lossless_webp` | string | `"false"`, `"true"` | `"true"` | **String enum, not boolean** (applies with `"webp"`) |
| `dpi` | integer | 72–300 | `300` | Print metadata |

**Recipe — clean catalog shot, auto-generated model, budget tier:**
```json
{
  "input_image_1": "https://cdn.example.com/diver-watch.jpg",
  "is_lifestyle": false,
  "is_closeup": true,
  "gender": "male",
  "model_tier": "ViTryon SD - 1Mpx"
}
```

**Recipe — lifestyle scene, one look per run (submit several runs to compare looks, each charged separately):**
```json
{
  "input_image_1": "https://cdn.example.com/dress-watch.jpg",
  "is_lifestyle": true,
  "is_closeup": true,
  "user_instructions": "elegant evening cafe setting, warm light, model with dark hair and a tailored blazer",
  "model_tier": "ViTryon PRO (HQ) - 4Mpx"
}
```

**Recipe — your own model reference, wider framing to show the person:**
```json
{
  "input_image_1": "https://cdn.example.com/smart-watch.jpg",
  "input_image_2": "https://cdn.example.com/model-bare-wrist.jpg",
  "is_closeup": false,
  "is_lifestyle": true,
  "product_size": "S (Small)"
}
```

**Recipe — print-ready hero at maximum native resolution:**
```json
{
  "input_image_1": "https://cdn.example.com/luxury-chrono.jpg",
  "model_tier": "ViTryon PRO (HQ) - 16Mpx",
  "is_lifestyle": false,
  "extension": "jpg",
  "quality": 95,
  "dpi": 300,
  "aspect_ratio": "4:5"
}
```

## Common failures -> cause -> fix

- **"No watch found" / wrong-item detection** -> the image doesn't contain a recognizable complete watch (it shows a bracelet, fitness band without a face, ring, necklace, earrings, or loose parts) -> route to the right app (**Bracelet Try-ON**, **Rings Try On**, **Necklace Try - On**, **Earrings Try On**), or for parts retouch with **Jewelry Retoucher Precision** / **Jewelry Retoucher Rescue**; for a genuine watch, pass a clear product-photo URL of the fully assembled piece.
- **Watch renders too large or too small on the wrist** -> scale mismatch -> set `product_size` to the real size (`"XS (Extra Small)"` … `"XL (Extra Large)"`) and re-run; also make sure the source shows the whole watch straight-on without heavy cropping or extreme perspective, so proportions are read correctly.
- **Result placed the watch oddly / a pocket watch on a wrist** -> the app is optimized for wrist watches and forces wrist placement -> for a pocket watch on a chain, use **Necklace Try - On** with placement described in instructions.
- **Placement interference or artifacts near the wrist** -> the model reference already wears a watch or bracelets on the placement wrist, or a sleeve fully covers it -> pass a reference with a bare, visible placement wrist, or omit `input_image_2` entirely.
- **Output doesn't match the reference's framing** -> toggle/reference mismatch: wide reference + `is_closeup: true` crops to the wrist; close-up reference + `is_closeup: false` invents more of the person; lifestyle reference + `is_lifestyle: false` strips the scene; studio reference + `is_lifestyle: true` invents one -> align both booleans with the reference's actual framing and setting.
- **Model isn't the exact person from the reference** -> expected pixel-preservation, but the reference is a style reference and the photo is regenerated -> expected behavior; the result is similar, not identical — communicate this rather than re-running.
- **Wrong watch chosen from a multi-watch photo** -> ambiguous source -> crop to the single watch before hosting, or disambiguate in `user_instructions` ("use the gold one").
- **Muddy dial markings / lost strap texture** -> soft or small source image, or the budget tier at large display sizes -> pass a ≥2000px-smallest-side URL, crop tighter, and step up to `"ViTryon PRO (HQ) - 4Mpx"` or `"ViTryon PRO (HQ) - 16Mpx"`.
- **Result is off-brief in styling** -> placement commands in `user_instructions` ("place the watch on the model") confuse generation -> restrict instructions to model/scene preferences (hair, skin, lighting, styling, environment with `is_lifestyle: true`) or leave blank.
- **Watch looks scratched/dirty in an otherwise good result** -> the app reproduces the watch as-is; debris can even be misread as detail -> clean and re-shoot, or run **Jewelry Retoucher Precision** (clean photo) / **Jewelry Retoucher Rescue** (worn piece) first, then try on the retouched output's URL.

## Cost notes

- **Base: 20 credits** at the default `"ViTryon SD - 1Mpx"` tier.
- `model_tier` surcharges: `"ViTryon PRO (HQ) - 4Mpx"` **+20** (total 40); `"ViTryon PRO (HQ) - 16Mpx"` **+60** (total 80). No other parameter carries a credit rule.
- Each run returns one result and is **charged once** — e.g. comparing 4 looks at the 4Mpx tier takes 4 separate runs = 4 × 40 = 160 credits.
- Discounted regeneration pricing exists for re-running: 7 credits base, with tier-specific regeneration rates of 8 (4Mpx) and 19 (16Mpx) attached to the PRO rules — use `estimate_run_cost` for the exact charge on any run.
- Economical workflow: explore composition/model/scene at `"ViTryon SD - 1Mpx"` (20/run), then produce the final at a PRO tier; submit extra runs only when genuinely comparing looks, since each run is charged separately.
