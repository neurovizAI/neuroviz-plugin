# All In One Video Suite (SD) — Agent Guide

_Reach for this app when you have ONE finished product photo and need an affordable short MP4 (5–15 seconds) with smooth camera motion — spins, zooms, or ambient movement. It is the SD model of the All In One Video Suite; **All In One Video Suite PRO** and **All In One Video Suite ULTRA** are the higher models._

## What it does — and what it will NOT do

**Does:** Animates a single photo (passed as a public HTTPS URL) into a short video. The photo is the first frame; NeuroViz writes the camera direction (360° or 90° rotation, zoom, or a locked camera with only the light and surroundings moving) from the movement keys and your plain-language instructions. Output is MP4 at **720p or 1080p**, any whole number of seconds from **5 to 15**. Every video carries a quiet soundtrack (soft ambience, no music, no speech).

**Will NOT do:**
- Use a second image. There is no last frame: it cannot transition between two pictures or end on a chosen frame.
- Guarantee a full 360° loop. A one-photo spin may cover less than a full circle and may not end on its starting frame. For a correct back view, use **All In One Video Suite PRO** or **All In One Video Suite ULTRA** with a real photo of the back as the last frame.
- Show the true back of a piece. It has only seen the front, so the reverse side is invented.
- Retouch or edit the photo. The video inherits every flaw in the source.
- Produce a silent file or music. The soundtrack is always quiet ambience.

## Choose it / don't choose it

**Choose it for:**
- Marketplace and product-page videos from one photo, at the lowest cost
- Social clips featuring a single piece
- Quick previews of a motion idea before a premium render

**Don't choose it when:**
- You need a first-frame-to-last-frame transition or the real back of the piece → **All In One Video Suite PRO** (front photo + real back photo + `back_side_image`)
- You need 2K detail or optional sound design → **All In One Video Suite PRO**
- You need up to 4K, a fixed aspect ratio such as 9:16, or a lifestyle clip with a person moving → **All In One Video Suite ULTRA**
- The photo first needs retouching → **Jewelry Retoucher Precision** (clean, sharp source) or **Jewelry Retoucher Rescue** (rough or worn piece), then come back here

## Inputs that make or break the result

- **One clear hero subject**, cropped close, at least **800px on the smallest side**. A tiny product in a large empty background gives an unfocused camera move.
- **A real, finished photograph.** Text graphics, sketches and abstract images fail subject detection; dust and glare in the source are animated, not removed.
- **Right orientation and framing** before hosting the image — there is no in-app editing on this path.
- **Plain-language instructions.** Describe the motion like you would to a camera operator ("slow push in on the stone"), not as a keyword prompt.

## Parameter playbook

| Parameter | Type | Required | Default | Values / notes |
|---|---|---|---|---|
| `first_frame` | image_url | yes | — | Public HTTPS URL; the video's first frame; ≥800px smallest side |
| `movement` | string | no | `""` | Keys `rotate_360`, `rotate_90`, `zoom`, `no_camera_motion`, comma-joined. Rotations and `no_camera_motion` exclude each other; `zoom` combines with a rotation. Empty = the app directs the motion |
| `resolution` | string | no | `"720p"` | `"720p"` or `"1080p"` — exact strings |
| `video_duration` | integer | no | `5` | Whole seconds, 5–15 |
| `custom_instructions` | string | no | `""` | Plain-language motion direction |

**Recipe 1 — cheapest default clip:**
```json
{"first_frame": "https://cdn.example.com/ring-hero.jpg"}
```

**Recipe 2 — 360° spin, 8 seconds, 1080p:**
```json
{"first_frame": "https://cdn.example.com/pendant.jpg", "movement": "rotate_360", "resolution": "1080p", "video_duration": 8}
```

**Recipe 3 — product stays still, light moves:**
```json
{"first_frame": "https://cdn.example.com/perfume.jpg", "movement": "no_camera_motion", "custom_instructions": "soft window light drifts slowly across the bottle"}
```

## Common failures -> cause -> fix

| Failure | Cause | Fix |
|---|---|---|
| "We couldn't find a subject" rejection | No clear photographic subject | Pass a real photo with one visible product |
| Soft video, missing detail | Low-resolution, blurry or screenshot source | Pass the original high-resolution photo; consider `"1080p"` |
| The spin does not end exactly where it started | The SD model has no last frame to pin the ending | Use **All In One Video Suite PRO** with `movement: "rotate_360"` and no last frame (it pins the start as the end), or accept the near-loop |
| Invented back of the piece | Only the front was ever seen | **All In One Video Suite PRO** with a real back photo as `last_frame` and `back_side_image: true` |
| Motion is not what was asked | Keyword-style instructions, or none at all | Write one plain sentence, or set an explicit `movement` key |
| `resolution` rejected | Wrong spelling (e.g. `"1080P"`) | Use exactly `"720p"` or `"1080p"` |

## Cost notes

- **Base: 40 credits** = 720p for 5 seconds. Price is set by `resolution` × `video_duration`; `movement` and instructions are free.
- **720p: 8 credits per second** — 5 s = 40, 10 s = 80, 15 s = 120.
- **1080p: 24 credits per second** — 5 s = 120, 10 s = 240, 15 s = 360.
- Off-spec values are rejected before any charge. Check `estimate_run_cost` before longer 1080p runs.
