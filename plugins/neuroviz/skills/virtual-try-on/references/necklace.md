# Necklace Try - On — Agent Guide

_Reach for this app to put a necklace, pendant, chain, or choker from a product photo onto a model's neck — the everyday workhorse for Etsy/Amazon/Shopify listings and day-to-day social content._

## What it does — and what it will NOT do

**Does:**
- Places one necklace product (chain, pendant, choker, statement piece, or a layered look shot together) onto a model's neck and chest.
- Model handling: pass your own model reference URL via `model_input`, or omit it and a model is generated. The generated model looks like the same person as the reference, but the photo is regenerated — pose, framing, and fine details may differ.
- Scale control via `product_size` (XS–XL), neck-and-chest framing via `is_closeup`, lifestyle vs. studio background via `is_lifestyle`, polished vs. natural model finish via ` beauty_look` (HQ tiers only).
- Three quality tiers up to 16 Mpx native, one result per run, aspect-ratio control, PNG/JPG/WebP output with DPI and JPEG-quality settings.

**Will NOT do:**
- Retouch, polish, or enhance the necklace — it is placed exactly as it appears in the source photo.
- Change the design — metal, stones, pendant, or chain length stay as photographed. (To hang a pendant on a different chain, send that chain as `chain_photo`.)
- Place brooches, pins, or non-necklace chest pieces; or rings, earrings, bracelets, watches.
- Preserve the model's photo exactly — the reference is a likeness guide, not a locked image.
- Guarantee exact pixel placement — for manual position/size control there is a dedicated Studio app (below).

## Choose it / don't choose it

| Situation | Verdict |
|---|---|
| Necklace/pendant/chain/choker product shot -> on-model listing photo | **Choose it** |
| Layered necklace look (all pieces in one photo, with an instruction) | **Choose it** |
| Rings | Use **Rings Try On** |
| Earrings | Use **Earrings Try On** |
| Bracelets or bangles | Use **Bracelet Try-ON** |
| Watches | Use **Watch Try-On** |
| Brooches, pins, non-necklace chest-worn pieces | Use **Product + Model Studio** |
| Several jewelry types on one model in one run | Use **Jewelry Set Try-On** |
| Exact, manual placement/size on the model (or the piece keeps getting cut off) | Use **Necklace Try-On Studio** — position and resize the necklace on a schematic model before generating |
| Necklace needs cleanup/polish first | **Jewelry Retoucher Precision** (clean, sharp photo) or **Jewelry Retoucher Rescue** (rough or worn) first, then return |
| Change the necklace's metal, stones, or length | **Jewelry Modifier Pro** first, then return |

## Inputs that make or break the result

- `jewelry_photo_1` (required), `model_input` (optional) and `chain_photo` (optional) are publicly reachable HTTPS URLs.
- `chain_photo` (optional) — a chain on its own (no pendant). The pendant from the main photo is hung on this chain, replacing any chain it was photographed with; exactly one chain appears. Only send it when the pendant has no chain or its chain should change. Never send the same image as the main photo: the run is refused. Chain length follows `user_instructions` when given, otherwise a natural length for the pendant.
- **Clean product shot on a plain background** — not the necklace being worn or on a bust; extraction from worn shots is unreliable.
- **Sharp, high-resolution, tightly framed.** If the necklace is small in the frame, crop close before hosting and pass the cropped version's URL — unresolved fine detail (delicate chains, small engraving) gets reconstructed and may change. Use original files, never screenshots or recompressed web images.
- **Show true proportions:** whole piece visible, straight-on, no heavy cropping of the piece itself, no extreme perspective — this is how the app reads its real size.
- **No glare:** even, diffused light so chain, setting, and stones read clearly.
- **Model reference:** single person, ideally a bare neck (an existing necklace interferes with placement) and a visible neckline — open collar, V-neck, scoop neck. A fully covered neck/chest blocks correct placement; a high collar pushes the necklace out of its natural position. When in doubt, omit `model_input`.
- If the photo contains multiple necklaces, disambiguate in `user_instructions` ("use the gold one" / "layer all necklaces together") or crop to the one you want before hosting.

**Pre-flight checklist before spending credits:**
1. Clean product shot of the necklace on a plain background — not worn, not on a bust, not a screenshot.
2. Whole piece visible straight-on, sharp, filling the frame (crop before hosting if not), no glare.
3. `product_size` reflects the piece's real-world scale.
4. If `model_input` is set: one person, bare neck, visible neckline; otherwise omit it.
5. `is_closeup`/`is_lifestyle` match the intended shot; `user_instructions` holds model preferences only.

## Parameter playbook

Exact names and values from the schema. Omitted optionals use their defaults.
**Warning:** the beauty-look parameter name is literally `" beauty_look"` — with a leading space. Copy it verbatim, including the space.

| Parameter | Type | Default | Values / range |
|---|---|---|---|
| `jewelry_photo_1` | image_url | — | required; necklace, pendant, or chain |
| `chain_photo` | image_url | empty | optional; a chain on its own to hang the pendant on (replaces the pendant's own chain) |
| `product_size` | string | `"M (Medium)"` | `"XS (Extra Small)"`, `"S (Small)"`, `"M (Medium)"`, `"L (Large)"`, `"XL (Extra Large)"` |
| `model_input` | image_url | empty | optional model reference (single person, bare neck preferred) |
| `gender` | string | `"female"` | `"female"`, `"male"` |
| `is_closeup` | boolean | `true` | `true` = focus on neck and chest, `false` = show more of the model |
| `is_lifestyle` | boolean | `false` | `true` = lifestyle background, `false` = clean studio |
| ` beauty_look` | boolean | `true` | polished editorial finish; applies only to HQ tiers |
| `model_tier` | string | `"ViTryon SD - 1Mpx"` | `"ViTryon SD - 1Mpx"`, `"ViTryon PRO (HQ) - 4Mpx"`, `"ViTryon PRO (HQ) - 16Mpx"` |
| `aspect_ratio` | string | `"auto"` | `"auto"`, `"1:1"`, `"1:4"`, `"1:8"`, `"2:3"`, `"3:2"`, `"3:4"`, `"4:1"`, `"4:3"`, `"4:5"`, `"5:4"`, `"8:1"`, `"9:16"`, `"16:9"`, `"21:9"` |
| `user_instructions` | string | empty | model preferences only (hair, skin, lighting, styling), any language |
| `extension` | string | `"png"` | `"png"`, `"jpg"`, `"webp"` |
| `lossless_webp` | string | `"true"` | `"false"`, `"true"` (WebP only) |
| `dpi` | integer | `300` | 72–300 |
| `quality` | integer | `80` | 50–100 (JPEG only) |

Tier guide: `"ViTryon SD - 1Mpx"` ≈ 1024x1024 drafts; `"ViTryon PRO (HQ) - 4Mpx"` ≈ 2000x2000; `"ViTryon PRO (HQ) - 16Mpx"` ≈ 4000x4000 native. PRO (HQ) tiers are the higher-precision option and the only ones where ` beauty_look` applies.

**Recipe 1 — standard catalog close-up, generated model, cheap draft:**
```json
{"jewelry_photo_1": "https://example.com/pendant.jpg"}
```
(Defaults give a close-up on a studio background, female model, SD tier.)

**Recipe 2 — final listing image, petite pendant, portrait crop:**
```json
{"jewelry_photo_1": "https://example.com/dainty-pendant.jpg", "product_size": "S (Small)", "model_tier": "ViTryon PRO (HQ) - 4Mpx", "aspect_ratio": "4:5"}
```

**Recipe 3 — lifestyle wide shot with own model reference, statement piece:**
```json
{"jewelry_photo_1": "https://example.com/statement-necklace.jpg", "product_size": "L (Large)", "model_input": "https://example.com/model-vneck-bare-neck.jpg", "is_closeup": false, "is_lifestyle": true, "model_tier": "ViTryon PRO (HQ) - 16Mpx", "user_instructions": "soft golden-hour lighting"}
```

**Recipe 4 — natural-look male model, chain, print-ready JPG:**
```json
{"jewelry_photo_1": "https://example.com/curb-chain.jpg", "gender": "male", " beauty_look": false, "model_tier": "ViTryon PRO (HQ) - 4Mpx", "extension": "jpg", "quality": 95}
```

## Common failures -> cause -> fix

- **"No necklace found" / input rejected** -> image does not read as a necklace product shot -> pass a clear product photo of the necklace or pendant on a plain background.
- **Wrong jewelry type in the photo** -> this app handles necklaces only -> route rings to **Rings Try On**, earrings to **Earrings Try On**, bracelets to **Bracelet Try-ON**, watches to **Watch Try-On**, brooches/pins to **Product + Model Studio**, multi-type combos to **Jewelry Set Try-On**.
- **Necklace too large or too small on the model** -> scale selector mismatch -> set `product_size` to match the real piece (`"L (Large)"`/`"XL (Extra Large)"` for oversized, `"S (Small)"`/`"XS (Extra Small)"` for delicate) and re-run. If the selector still cannot land the proportions, switch to **Necklace Try-On Studio** for manual position + size control. Also verify the source shows the whole piece straight-on without extreme perspective.
- **Necklace cut off at the frame edge** -> framing/placement limits of the automatic try-on -> re-run with `is_closeup` toggled, or use **Necklace Try-On Studio**, which is built for exact placement and sizing.
- **Pendant shape, chain style, stones, engraving, or clasp changed** -> the app reconstructs what it cannot resolve: piece small in frame, soft focus, glare hiding parts, very fine detail, or a compressed/screenshot source -> crop tighter before hosting, use the sharpest original file, diffuse the lighting; for detail that must survive exactly, retouch first with **Jewelry Retoucher Precision** (or **Jewelry Retoucher Rescue** for rough/worn photos) and run the clean result through the try-on.
- **Necklace looks dull or worn in the result** -> the app places it exactly as photographed, no retouching -> retouch the product photo first (**Jewelry Retoucher Precision** / **Jewelry Retoucher Rescue**), then re-run.
- **Placement looks wrong near clothing** -> the model reference's neck/chest was covered or had a high neckline -> use a reference with a visible neckline (open collar, V-neck, scoop neck) or omit `model_input`.
- **Old necklace from the reference interferes** -> reference model already wearing a chain -> use a bare-neck reference or omit `model_input`.
- **Result framing does not match the reference** -> `is_closeup` contradicts the reference: `true` crops to neck/chest even from a wide reference; `false` regenerates more of the model from a neck close-up reference -> align `is_closeup` with the shot you want, and keep `user_instructions` consistent with it.
- **Background does not match the reference** -> `is_lifestyle` contradicts the reference type -> `true` for a lifestyle environment, `false` for clean studio.
- **Skin/makeup too polished or too plain** -> ` beauty_look` controls the finish (`true` = editorial, `false` = natural) — note it only takes effect on the PRO (HQ) tiers; on `"ViTryon SD - 1Mpx"` it changes nothing.
- **Model does not match the reference exactly** -> expected: the person's likeness is kept but the photo is regenerated; pose, framing, and details differ -> no configuration preserves the reference photo pixel-for-pixel.
- **Try-on instructions confused the output** -> directives like "place the necklace on the model" in `user_instructions` fight the automatic placement -> keep `user_instructions` to model preferences only, or leave it empty for the best dynamic result.

## Cost notes

- **Base: 20 credits** per run; **regeneration: 7 credits**.
- `model_tier` `"ViTryon PRO (HQ) - 4Mpx"`: **+20 credits** (+8 on regeneration) -> 40 per run.
- `model_tier` `"ViTryon PRO (HQ) - 16Mpx"`: **+60 credits** (+19 on regeneration) -> 80 per run.
- `"ViTryon SD - 1Mpx"` adds nothing -> 20 per run.
- Each run returns one result and is charged once; to compare several results, submit several runs, each charged separately (e.g., 4 SD runs = 80; 2 runs at 4Mpx = 80).
- Cost-efficient loop: settle `product_size`, framing, and model look at `"ViTryon SD - 1Mpx"`, then produce the deliverable once at a PRO (HQ) tier (also required for ` beauty_look` to apply).
- Use `estimate_run_cost` for the exact number before committing a run.
