# Rings Try On — Agent Guide

_Reach for this app when a ring product photo needs to be shown worn on a realistic model hand — for listings, social posts, or lookbooks._

## What it does — and what it will NOT do

**Does:** Places a ring from a product photo (public HTTPS URL) onto a model's hand. The model can be generated from scratch, or guided by an optional model reference photo. Control gender, hand (left/right), finger, ring size (XS–XL), close-up vs wider framing, white-studio vs lifestyle environment, and output tier/resolution. The ring is placed exactly as it appears in the photo — same design, same finish.

**Will NOT do:**
- Retouch, polish, or enhance the ring. Whatever the product photo shows is what lands on the hand.
- Change the ring's design — metal, stones, shape, size of the piece itself.
- Handle other jewelry types (earrings, necklaces, bracelets, watches) or multiple jewelry types in one run.
- Reproduce a model reference exactly. The reference is a STYLE GUIDE: the output model looks similar, but pose, hand shape, and details will differ — that freedom is necessary for correct ring placement.

## Choose it / don't choose it

**Choose it for:**
- Product photos of rings (single or a pair) on plain backgrounds → on-hand context shots
- Engagement, statement, and fashion rings for e-commerce listings and social content
- Trying several model styles, hand poses, or crops on the same ring (submit several runs; each returns one result and is charged separately)

**Don't choose it when:**
- The ring photo needs retouching or cleanup first → **Jewelry Retoucher Precision** (clean, sharp photo) or **Jewelry Retoucher Rescue** (rough photo or worn piece), then return here with the retouched output's URL
- The design itself should change (metal, stones, shape) → **Jewelry Modifier Pro** first, then try on the modified result
- The photo shows earrings → **Earrings Try On**; necklace or pendant → **Necklace Try - On**; bracelet or bangle → **Bracelet Try-ON**; watch → **Watch Try-On**
- Several jewelry types must appear together on one model → **Jewelry Set Try-On**

## Inputs that make or break the result

- **`jewelry_photo_1`: a clear product photo of a ring, outer surface visible.** A shot showing only the inner band (the side that touches the finger) cannot be placed correctly — the app needs the decorated outer surface. A three-quarter or front angle that matches how the ring reads when worn gives the most predictable result.
- **Orientation matters for placement.** If placement comes out wrong or rotated, rotate the ring image so its orientation matches how it sits when worn, host the rotated version, and pass that URL.
- **Fill the frame.** A ring that is tiny in the photo with lots of empty background loses fine detail. Crop close before hosting and pass the cropped version's URL.
- **Sharpness carries through.** A soft photo still runs, but fine details are lost in the result. For sharp output, pass a higher-quality source.
- **One ring, or say which.** With multiple rings in the photo and no instructions, the choice is ambiguous. Either crop to the one ring, or set `user_instructions` like "use the ring on the left" or "place all rings together".
- **Model reference (optional): bare hands work best.** Rings already worn in the reference can interfere with placement. Leave `model_input` empty to have the model generated. Match the reference type to `is_closeup`: a hand close-up reference pairs with `is_closeup: true`; a wider model shot pairs with `is_closeup: false` — mismatches mean more of the output is generated rather than guided by the reference.
- **Clean piece, neutral light.** Colored light or nearby colored surfaces shift metal/stone color; dirt specks in channels or prongs can be read as detail. Clean and neutrally lit sources keep the ring true.

## Parameter playbook

| Parameter | Type | Required | Default | Values / notes |
|---|---|---|---|---|
| `jewelry_photo_1` | image_url | yes | — | Public HTTPS URL; good quality, angle showing the ring's features |
| `ring_gender` | string | no | `"female"` | Enum: `"male"`, `"female"` |
| `ring_finger` | string | no | `"ring"` | Enum: `"thumb"`, `"index"`, `"middle"`, `"ring"`, `"pinky"`. For any non-ring finger, providing `model_input` is highly recommended |
| `ring_hand` | string | no | `""` | Enum: `""`, `"left"`, `"right"` (empty = unspecified) |
| `model_input` | image_url | no | `""` | Optional model reference (style guide, not an exact copy). Empty = model generated |
| `is_lifestyle` | boolean | no | `false` | `true` = lifestyle environment; `false` = white studio background |
| `is_closeup` | boolean | no | `true` | `true` = only the hand with the ring; `false` = model's face may be visible |
| `is_glamour` | boolean | no | `true` | "Beauty Look" polish — applies only on the HQ tiers |
| `model_tier` | string | no | `"ViTryon SD - 1Mpx"` | Enum: `"ViTryon SD - 1Mpx"`, `"ViTryon PRO (HQ) - 4Mpx"`, `"ViTryon PRO (HQ) - 16Mpx"`. HQ tiers are newer, more precise, native resolution up to 16Mpx |
| `product_size` | string | no | `"M (Medium)"` | Enum: `"XS (Extra Small)"`, `"S (Small)"`, `"M (Medium)"`, `"L (Large)"`, `"XL (Extra Large)"` — reference size of the ring on the hand |
| `user_instructions` | string | no | `""` | Leave blank; use only for something more specific than "put a ring on a model". Multi-language |
| `quality` | integer | no | `80` | 50–100 (JPEG quality) |
| `dpi` | integer | no | `300` | 72–300 |
| `extension` | string | no | `"png"` | Enum: `"png"`, `"jpg"`, `"webp"` |
| `lossless_webp` | string | no | `"true"` | Enum: `"false"`, `"true"` (strings) |
| `aspect_ratio` | string | no | `"auto"` | Enum: `"auto"`, `"1:1"`, `"1:4"`, `"1:8"`, `"2:3"`, `"3:2"`, `"3:4"`, `"4:1"`, `"4:3"`, `"4:5"`, `"5:4"`, `"8:1"`, `"9:16"`, `"16:9"`, `"21:9"`. `"auto"` matches the crop's aspect |

**Recipe 1 — clean catalog hand shot (cheapest, defaults do the work):**
```json
{"jewelry_photo_1": "https://cdn.example.com/solitaire.jpg"}
```

**Recipe 2 — lifestyle social shot guided by a model reference, HQ, portrait crop:**
```json
{"jewelry_photo_1": "https://cdn.example.com/gold-band.jpg", "model_input": "https://cdn.example.com/model-ref.jpg", "is_lifestyle": true, "is_closeup": false, "model_tier": "ViTryon PRO (HQ) - 4Mpx", "aspect_ratio": "4:5"}
```

**Recipe 3 — men's signet ring, index finger of the left hand, large size:**
```json
{"jewelry_photo_1": "https://cdn.example.com/signet.jpg", "ring_gender": "male", "ring_finger": "index", "ring_hand": "left", "product_size": "L (Large)", "model_input": "https://cdn.example.com/male-hand-ref.jpg"}
```

**Recipe 4 — A/B candidate at max native resolution (one result per run; submit it several times to compare, each run charged separately):**
```json
{"jewelry_photo_1": "https://cdn.example.com/halo-ring.jpg", "model_tier": "ViTryon PRO (HQ) - 16Mpx"}
```

## Common failures -> cause -> fix

| Failure | Cause | Fix |
|---|---|---|
| "Couldn't find a ring" rejection | The image shows no ring — or shows earrings/necklace/bracelet/watch | Pass a clear ring product photo; for other types route to **Earrings Try On**, **Necklace Try - On**, **Bracelet Try-ON**, **Watch Try-On**; mixed pieces → **Jewelry Set Try-On** |
| Ring too large or too small on the hand | `product_size` doesn't match the real piece | Set `product_size` to match (`"L (Large)"`/`"XL (Extra Large)"` for massive rings, `"S (Small)"`/`"XS (Extra Small)"` for tiny ones) and re-run — this resolves most proportion issues. A clear, straight-on, uncropped source also helps proportions read correctly |
| Placement incorrect / ring sits at an odd angle | Source photo's orientation or angle doesn't match how the ring is worn; or only the inner band is visible | Rotate the ring image to match worn orientation and pass that URL; use a three-quarter or front-angle photo showing the outer, decorated surface |
| Ring details changed or muddy in the result | Source too soft, ring too small in frame, or debris misread as stones | Pass a sharper, tighter-cropped URL; clean the piece before shooting; a higher `model_tier` preserves more fine detail |
| Metal or stone color shifted | Colored light/background reflecting into the metal, or glare on stones | Reshoot in neutral light on a neutral background; you may also state the true color in `user_instructions` ("the band is white gold — keep it cool silver-toned") |
| Output model doesn't match the reference | Expected behavior — `model_input` is a style reference; pose, hand shape, and details always differ | If similar-but-not-identical is acceptable, proceed; exact model preservation is not supported |
| Result crops to the hand but a wider scene was wanted (or vice versa) | `is_closeup` conflicts with the reference type or the instructions | Wider shot → `is_closeup: false`; hand-focused → `is_closeup: true`; keep `user_instructions` consistent with the chosen framing |
| Stray or doubled rings near the placement | The model reference's hands already wear rings | Pass a bare-hands reference URL, or omit `model_input` |
| Wrong ring picked from a multi-ring photo | Ambiguous source with no guidance | Crop to the intended ring, or set `user_instructions` ("use the ring on the left" / "place all rings together") |
| Request to polish/fix or redesign the ring | Out of scope — this is placement only | Retouch first: **Jewelry Retoucher Precision** or **Jewelry Retoucher Rescue**; redesign: **Jewelry Modifier Pro**; then re-run the try-on with the new output's URL |

## Cost notes

- **Base: 20 credits** at the default `"ViTryon SD - 1Mpx"` tier.
- `model_tier` changes the price:
  - `"ViTryon PRO (HQ) - 4Mpx"` → **+20** (40 total)
  - `"ViTryon PRO (HQ) - 16Mpx"` → **+60** (80 total)
- **Each run returns one result and is charged once**; to compare several looks, submit several runs, each charged separately (e.g. 3 runs at the 4Mpx tier ≈ 3 x 40 = 120 credits).
- **Regeneration**: 7 credits base; HQ tiers carry their own regeneration surcharges (8 for 4Mpx, 19 for 16Mpx).
- `quality`, `dpi`, `extension`, `lossless_webp`, `aspect_ratio`, framing booleans, and instructions do not affect price.
- Enum strings must be copied exactly (case and spacing included, e.g. `"M (Medium)"`) — off-spec values are rejected before any charge. Use `estimate_run_cost` for the exact figure.
