# All In One Video Suite PRO — Agent Guide

_Reach for this app when the video must look premium at a moderate price: one or two frames of the same subject, smooth controlled camera work, up to 2K detail, a correct front-to-back turn, and optional sound design at no extra cost. It is the PRO model of the All In One Video Suite, between **All In One Video Suite** (SD) and **All In One Video Suite ULTRA**._

## What it does — and what it will NOT do

**Does:**
- Animates a single First Frame, or transitions First Frame → Last Frame across the clip (before/after, angle to angle, studio to lifestyle).
- **Turns from one photo**: with `movement: "rotate_360"` and no last frame, the camera turns slowly in one direction. The turn can cover less than a full circle and may not end on the first frame, and the unseen back is invented. For a real front-to-back turn, supply the back photo (next point).
- Produces a **correct front-to-back turn** when the Last Frame is a real photo of the BACK of the same item and `back_side_image` is `true`.
- Outputs MP4 at **HD** (the native render) or **2K** (the same clip redrawn at about 3.7 megapixels for finer detail), any whole number of seconds from **5 to 15**. The canvas follows the first frame's shape.
- Every video has a soundtrack. `generate_audio: true` asks for sound design that fits the scene (no speech); `false` keeps it to quiet ambience with no music or speech. Either way it costs nothing extra.

**Will NOT do:**
- Retouch, clean or redesign the product — every flaw in a frame is inherited.
- Blend two unrelated images gracefully — use two states of the same subject.
- Output 4K, a fixed aspect ratio, or a fully silent file → **All In One Video Suite ULTRA** (4K, aspect presets, audio off).
- Render instantly: a PRO clip takes from about half a minute (HD, 5 s) to about 3 minutes (2K or 15 s).

## Choose it / don't choose it

| Situation | Verdict |
|---|---|
| Premium product video where camera control and product fidelity matter | **Choose it** |
| A slow turn from one photo (not a guaranteed full loop) | **Choose it** (`rotate_360`, no last frame) |
| Correct back-side turn | **Choose it** (front + real back photo + `back_side_image: true`) |
| First-to-last transition of the same subject | **Choose it** |
| Cheapest possible clip from one photo | Don't — **All In One Video Suite** (SD) |
| 4K, 9:16/16:9 delivery, or a lifestyle clip with a person in motion | Don't — **All In One Video Suite ULTRA** |
| Frame needs cleanup first | Don't — **Jewelry Retoucher Precision** / **Jewelry Retoucher Rescue** first, then animate |

## Inputs that make or break the result

- `first_frame` is required: a sharp, clearly framed shot, **≥800px on the smallest side**, as a public HTTPS URL. Without it nothing runs.
- **Same subject in both frames.** For transitions and back-side turns, shoot both frames at the same distance, framing and lighting, or the turn reads as a jump cut.
- **2K magnifies the source.** Soft or compressed frames look worse at 2K, not better — preview at HD first.
- **Instructions in plain language**: name the one camera move and how far it goes ("a slow quarter-turn to the right while moving closer"), not a list of keywords.

## Parameter playbook

| Parameter | Type | Values / range | Default | Notes |
|---|---|---|---|---|
| `first_frame` | image_url | HTTPS URL | — | **Required** |
| `last_frame` | image_url | HTTPS URL | `""` | Optional; **required when `back_side_image` is `true`** |
| `back_side_image` | boolean | `true` / `false` | `false` | `true` = Last Frame is the real BACK; one continuous front-to-back turn |
| `movement` | string | `rotate_360`, `rotate_90`, `zoom`, `no_camera_motion`, comma-joined | `""` | Rotations and `no_camera_motion` exclude each other; `zoom` combines with a rotation |
| `resolution` | string | `"HD"`, `"2K"` | `"HD"` | Exact strings; the main cost driver with duration |
| `video_duration` | integer | 5–15 | `5` | Whole seconds |
| `generate_audio` | boolean | `true` / `false` | `false` | Sound design on/off; free |
| `custom_instructions` | string | free text | `""` | Plain-language direction |

**Recipe — slow 360° turn from one photo at 2K, 10 seconds:**
```json
{"first_frame": "https://cdn.example.com/ring-hero.jpg", "movement": "rotate_360", "resolution": "2K", "video_duration": 10}
```

**Recipe — correct back-side turn:**
```json
{"first_frame": "https://cdn.example.com/locket-front.jpg", "last_frame": "https://cdn.example.com/locket-back.jpg", "back_side_image": true, "video_duration": 8}
```

**Recipe — studio-to-lifestyle transition with sound design:**
```json
{"first_frame": "https://cdn.example.com/watch-white.jpg", "last_frame": "https://cdn.example.com/watch-on-wrist.jpg", "generate_audio": true, "custom_instructions": "the camera glides in as the scene warms into the wrist shot"}
```

## Common failures -> cause -> fix

- **Run fails asking for a Last Frame** -> `back_side_image: true` without `last_frame` -> add the real back photo, or set the flag to `false`.
- **Odd morph between frames** -> the two frames show different subjects -> use two states of the same item, or drop `last_frame` and use `movement`.
- **Turn reads as a cut** -> front and back shot differently -> re-shoot both sides with matching distance and light.
- **Camera move too small** -> instructions without an amount -> say how far ("a full half-turn", "a quarter-turn") or set a `movement` key.
- **Unexpected music or voices** -> sound design on, or instructions that ask for them -> set `generate_audio: false`; describe only the motion.
- **Slow result** -> PRO renders take up to about 3 minutes at 2K or 15 s -> expected; poll the run or use the webhook instead of retrying (a retry is charged again).
- **2K looks soft** -> the source frame was soft -> pass the original high-resolution photo; preview at HD.

## Cost notes

- **Base: 80 credits** = HD for 5 seconds. Price is set by `resolution` × `video_duration`; `movement`, `back_side_image`, `generate_audio` and instructions are free.
- **HD: 16 credits per second** — 5 s = 80, 10 s = 160, 15 s = 240.
- **2K: 28 credits per second** — 5 s = 140, 10 s = 280, 15 s = 420.
- Confirm with `estimate_run_cost` before long 2K runs.
