# All In One Video Suite ULTRA — Agent Guide

_Reach for this app when the video must be top tier: up to 4K output, a fixed aspect ratio, synchronized audio (or none) at no extra credit cost, or a lifestyle clip featuring a person — while keeping single-frame animation, first-to-last transitions and correct back-side 360s. It is the ULTRA model of the All In One Video Suite, above **All In One Video Suite** (SD) and **All In One Video Suite PRO**._

## What it does — and what it will NOT do

**Does:**
- Animates a single First Frame, transitions First Frame -> Last Frame, and produces a **correct front-to-back 360 rotation** when the Last Frame is a real photo of the item's back and `back_side_image` is `true`.
- Outputs MP4 at **480p, 720p, 1080p, or 4k**, 5–15 seconds (integer seconds, not just three presets).
- **Audio included at no extra credit cost** — a synchronized AI-generated track (sound effects, ambience) when `generate_audio` is `true`.
- Especially strong on **lifestyle videos featuring people** — models and human motion are this app's specialty.
- Extra output control: `aspect_ratio` presets (vertical 9:16 for Reels/TikTok, etc.) and a `bitrate_mode` of `high` for larger, higher-fidelity files.

**Will NOT do:**
- Retouch or edit a frame — every input flaw is inherited and animated.
- Change the product's design in a frame.
- Show the true back of a piece from a single front photo — without a real back photo it must guess.
- Transition gracefully between two unrelated images — the morph looks strange.
- Beat **All In One Video Suite PRO** on price for HD/2K work — this app costs more at comparable settings.

## Choose it / don't choose it

| Situation | Verdict |
|---|---|
| Highest-quality product video, up to 4K | **Choose it** |
| Lifestyle clip with a model / human motion | **Choose it** — its standout strength |
| Audio wanted without paying extra credits for it | **Choose it** — audio is free here |
| Correct back-side 360 at premium resolution | **Choose it** (front photo + real back photo + `back_side_image`) |
| Vertical 9:16 or other fixed aspect-ratio delivery | **Choose it** — has an `aspect_ratio` control |
| Transition or back-side 360 where HD or 2K is enough | Don't — **All In One Video Suite PRO** does the same job for fewer credits |
| Quick economical clip from a single photo, no 4K/audio needs | Don't — **All In One Video Suite** (SD) |
| Frame needs cleanup or polish first | Don't — **Jewelry Retoucher Precision** (clean photo), **Jewelry Retoucher Rescue** (rough/worn), or **AI Photo Editor Pro – Retouching & Editing V2** first, then animate |
| Product design must change first | Don't — **Jewelry Modifier Pro** first, then animate the result |
| Two unrelated images as the two frames | Don't — use two states of the same subject |

## Inputs that make or break the result

- `first_frame` is required: a clear, identifiable shot, **at least 800px on its smallest side**, passed as a publicly reachable HTTPS URL (plain string).
- **The video inherits source quality.** Soft frames give soft video; screenshot-grade or heavily compressed web images give screenshot-grade video — especially punishing at 1080p/4k, where you pay premium credits to magnify flaws. Host originals.
- **Crop/orient before hosting.** Small subject in a sea of background, or a rotated frame? Fix the image first and pass the corrected URL — cheaper than re-generating.
- **Back-side 360:** the Last Frame must be a real photo of the BACK of the same item, shot at similar distance, framing, and lighting as the front, or the turn reads as a cut instead of one continuous move.
- **Transitions:** both frames must be the SAME subject in two states (angle, before/after, product-to-on-model). Unrelated subjects morph oddly.
- **People in frame:** this is the app to use, but the person must be clearly visible and well-lit in the source frame — it directs human motion from what it sees.
- A clean, debris-free product photo matters: specks and dust in the source can be misread as product detail and then animated.

## Parameter playbook

Exact schema names and values (base cost 85 credits; enum strings must match case-exactly — note lowercase `"4k"`; omitted optional parameters use their defaults):

| Parameter | Type | Values / range | Default | Notes |
|---|---|---|---|---|
| `first_frame` | image_url | HTTPS URL string | — | **Required.** ≥800px smallest side |
| `last_frame` | image_url | HTTPS URL string | `""` | Optional; **required when `back_side_image` is `true`** |
| `back_side_image` | boolean | `true`/`false` | `false` | `true` = Last Frame is the real BACK; directs one continuous front-to-back half-turn |
| `movement` | string | option keys: `rotate_360`, `rotate_90`, `zoom`, `no_camera_motion` | `""` | Optional. Multi-select: comma-join several keys (`"rotate_90,zoom"`); rotations and `no_camera_motion` exclude each other. An empty string defers to `custom_instructions`/automatic direction |
| `resolution` | string | `"480p"`, `"720p"`, `"1080p"`, `"4k"` | `"480p"` | Dominant cost driver (~17/34/83/171 credits per second respectively) |
| `aspect_ratio` | string | `"auto"`, `"21:9"`, `"16:9"`, `"4:3"`, `"1:1"`, `"3:4"`, `"9:16"` | `"auto"` | `auto` follows the input image's shape |
| `bitrate_mode` | string | `"standard"`, `"high"` | `"standard"` | `high` = larger, higher-fidelity files (no credit rule attached) |
| `video_duration` | integer | 5–15, step 1 | `5` | Whole seconds |
| `generate_audio` | boolean | `true`/`false` | `false` | Synchronized sound effects/ambience — **no extra credits** |
| `custom_instructions` | string | free text, multiline | `""` | Plain-language motion direction ("slow dolly in", "camera orbits the ring") |

**Recipe — 4K flagship product spin with audio (the full-premium run):**
```json
{
  "first_frame": "https://cdn.example.com/ring-hero.jpg",
  "movement": "rotate_360",
  "resolution": "4k",
  "video_duration": 10,
  "generate_audio": true,
  "bitrate_mode": "high"
}
```

**Recipe — cheapest possible concept preview (always run before a 1080p/4k final):**
```json
{
  "first_frame": "https://cdn.example.com/ring-hero.jpg",
  "movement": "zoom",
  "resolution": "480p",
  "video_duration": 5
}
```

**Recipe — vertical lifestyle clip with a model, for Reels/TikTok:**
```json
{
  "first_frame": "https://cdn.example.com/model-wearing-necklace.jpg",
  "movement": "no_camera_motion",
  "resolution": "1080p",
  "aspect_ratio": "9:16",
  "video_duration": 8,
  "generate_audio": true,
  "custom_instructions": "the model smiles and turns slightly toward the camera, soft golden-hour ambience"
}
```

**Recipe — correct back-side 360 at premium resolution:**
```json
{
  "first_frame": "https://cdn.example.com/locket-front.jpg",
  "last_frame": "https://cdn.example.com/locket-back.jpg",
  "back_side_image": true,
  "movement": "rotate_360",
  "resolution": "1080p",
  "video_duration": 10
}
```

## Common failures -> cause -> fix

- **360 spin shows an invented back** -> a single image never reveals the back, so the app guesses -> pass a real back photo as `last_frame` and set `back_side_image: true`; the turn then goes correctly front-to-back.
- **Back-side option set but the run makes no sense** -> `back_side_image: true` without a `last_frame`; the back-side turn needs the real back photo -> add `last_frame` or set the flag to `false`.
- **Subjects morph oddly mid-clip** -> the two frames show unrelated subjects; with a `last_frame` the app transitions the first into the last -> use two frames of the SAME subject (different angle, before/after, or the back); for single-image motion, drop `last_frame` and use `movement`.
- **Back-side turn reads as a jump-cut** -> front and back shot at different distances/lighting -> re-shoot both sides identically and pass the new URLs.
- **Camera does something generic instead of the intended move** -> no movement key and no instructions leaves the app to direct itself -> set explicit `movement` keys or describe the move plainly in `custom_instructions` ("slow dolly in", "orbit the ring").
- **Warped or fighting motion** -> multiple comma-joined `movement` keys competing in a short clip -> keep the single key that matters, or describe the combination in `custom_instructions` instead.
- **Run far more expensive than expected** -> cost scales with BOTH `resolution` and `video_duration`, and `"4k"` is by far the priciest -> preview at `"480p"`/5 s, then re-run the proven concept at the final resolution; check with `estimate_run_cost` first.
- **Soft video, missing fine detail** -> soft or low-resolution source frame; the video inherits it -> pass sharper, higher-resolution frame URLs (≥800px smallest side, ideally much more for 4k).
- **Result looks like an animated screenshot** -> the source was a screenshot or low-quality web image -> host and pass the original high-resolution photo.
- **Expected free audio but got silence** -> `generate_audio` defaults to `false` -> set it `true`; audio itself adds no credits in this app.

## Cost notes

- **Base: 85 credits**, plus a resolution×duration surcharge from the credit rules. `generate_audio`, `bitrate_mode`, `aspect_ratio`, and `movement` never change the price.
- Surcharge table (credits added to the base, by `resolution` / `video_duration`):

| Duration (s) | `480p` | `720p` | `1080p` | `4k` |
|---|---|---|---|---|
| 5 | +0 | +80 | +335 | +770 |
| 6 | +20 | +115 | +415 | +940 |
| 7 | +35 | +150 | +500 | +1115 |
| 8 | +50 | +180 | +585 | +1285 |
| 9 | +70 | +215 | +665 | +1455 |
| 10 | +85 | +250 | +750 | +1625 |
| 11 | +100 | +280 | +835 | +1800 |
| 12 | +120 | +315 | +915 | +1970 |
| 13 | +135 | +350 | +1000 | +2140 |
| 14 | +155 | +380 | +1085 | +2310 |
| 15 | +170 | +415 | +1165 | +2480 |

- Anchors: cheapest run 480p/5s = **85**; 720p/10s = **335**; 1080p/10s = **835**; 4k/10s = **1710**; most expensive 4k/15s = **2565**. Confirm with `estimate_run_cost` before premium runs.
- Preview economics: a 480p/5s concept test costs ~3% of a 4k/15s final — never skip it.
- If HD or 2K is all the job needs, **All In One Video Suite PRO** delivers the same transition/back-side capability for fewer credits (80 base at HD), with sound design included.
