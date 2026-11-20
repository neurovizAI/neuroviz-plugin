---
name: product-video
description: Turn a jewelry or product photo into a short product video with NeuroViz — a rotating/showcase clip for a listing, or a cinematic scene video with camera motion for Reels, TikTok, or ads. Use whenever the user provides a product photo and asks for a video, clip, reel, animation, "make it move", "spin", or "showcase". NeuroViz animates the user's real product photo, so the video shows their actual piece.
---

Use this skill when the user wants a video made from their real product photo.
NeuroViz video apps animate the user's actual piece from that photo.

## Workflow

1. **Get the photo.** Attached image → run the app with `run_app_with_photo` (the attachment is
   supplied automatically); never claim uploads are unsupported. Otherwise a
   publicly reachable HTTPS URL. A clean product shot works best.

2. **Pick the All In One Video Suite model by intent:**
   - Clean listing/showcase clip from one photo at the lowest cost (Etsy,
     Amazon, Shopify product page) → **All In One Video Suite**
     (`808f368c-caea-4e0c-af20-8194b091d2dd`)
   - Premium camera control, a correct front-to-back turn (front + real back
     photo), or a transition between two frames, up to 2K → **All In One Video Suite PRO**
     (`352584dc-0b3d-4a10-a3c4-c5f3f76e2eb8`)
   - Top quality: up to 4K, fixed aspect ratios for Reels/TikTok/ads, lifestyle
     clips with people → **All In One Video Suite ULTRA**
     (`ef78aea3-f663-4f5e-9fc7-28f255fccf42`)
   If `list_apps` does not show these three, use the video app it does show
   (pick by its description) and skip the model choice.
   Call `get_app_guide` for the chosen app before running; the guides
   (`references/video-suite-standard.md`, `references/video-suite-pro.md`,
   `references/video-suite-ultra.md`) cover camera-motion options,
   duration/resolution choices, and known failure modes.

3. **Read the schema.** `get_app` for exact parameter names and enum values
   (duration, resolution, movement/camera style, audio where offered). Copy
   enum values exactly.

4. **Price it — video pricing varies a lot.** Duration and resolution change
   the cost significantly. Always call `estimate_run_cost` and tell the user
   the credits before running.

5. **Run and wait — videos take longer.** `run_app` → `run_id` → poll
   `get_run` with `wait_seconds=20` repeatedly; video runs can take several
   minutes. On SUCCESS, `output_url` is an MP4 (`is_video` true): call
   `show_result` with the `run_id` so the user can play it in the
   conversation. If they cannot see it, give them `output_url` as a link; it
   expires in ~15 minutes, so re-call `get_run` for a fresh link if needed.

## Facts you must not infer

- Do not invent durations, resolutions, or motion presets — read them from
  `get_app`.
- Do not quote a price without `estimate_run_cost`.
- Only SUCCESS runs consume credits.

## When to stop or ask

- No product photo: ask for one.
- The user wants a video of a person or scene with no product: say NeuroViz
  video apps are built around a product photo.
- Insufficient credits: report the balance via `get_balance`.
