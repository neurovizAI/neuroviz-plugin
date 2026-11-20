---
name: virtual-try-on
description: Show a piece of jewelry worn on a model using NeuroViz virtual try-on — put a ring on a hand, earrings on an ear, a necklace on a neck, a bracelet on a wrist, a watch on a wrist. Use whenever the user provides a jewelry product photo and asks to see it "on a model", "worn", "on someone", "on a hand/neck/ear/wrist", or wants lifestyle/model shots for a listing. NeuroViz shows the user's real piece, taken from their product photo, on the model.
---

Use this skill when the user wants their actual jewelry shown on a person.
NeuroViz try-on apps place the user's real piece, taken from their product
photo, onto a model photo.

## Workflow

1. **Get the product photo.** Attached image → run the app with `run_app_with_photo` (the attachment is
   supplied automatically); never claim uploads are unsupported. Otherwise ask for a publicly reachable HTTPS URL. A clean
   product shot of the piece alone works best.

2. **Pick the app by jewelry type** — one app per category:
   - Ring → **Rings Try On** (`7587c544-92e2-45c6-afa7-298fd476ae79`)
   - Earrings → **Earrings Try On** (`791b5d3c-339d-406d-a477-e2504c434ed8`);
     for precise placement control there is also Earrings Try-On Studio
   - Necklace / pendant → **Necklace Try - On**
     (`e05f9ae0-7800-474a-936d-c4f68778156e`); premium hero shots →
     Necklace Try-On Studio
   - Bracelet / bangle → **Bracelet Try-ON** (`06c8c5d6-a007-4abf-b00c-3ce3cc10fa85`)
   - Watch → **Watch Try-On** (`fe6ff4f5-10e0-4561-a87d-6edbd131a296`)
   - A coordinated set → Jewelry Set Try-On
   Call `get_app_guide` for the chosen app before running: the guides
   (`references/rings.md`, `earrings.md`, `necklace.md`, `bracelet.md`,
   `watch.md`) cover model selection, sizing, and the common failures.

3. **Model photo.** If the user has not supplied a model image and the app
   takes one, call `list_system_images` — NeuroViz's curated preset models,
   hands and backgrounds — and use a suitable preset URL as the parameter
   value. Tell the user which preset you chose.

4. **Read the schema.** `get_app` for exact parameter names and enum values
   (gender, size/placement, output tier, etc.). Copy enum values exactly.

5. **Price it.** `estimate_run_cost` first; tell the user the credits.

6. **Run and wait.** `run_app` → `run_id` → poll `get_run` with
   `wait_seconds=20` until terminal. On SUCCESS, call `show_result` with the
   `run_id` so the user sees the try-on in the conversation (the image
   `get_run` attaches is visible to you only). Give `output_url` as a link only
   if the user asks or cannot see the result.

7. **If sizing or placement looks wrong,** consult the app's guide for the
   size/placement parameters rather than re-running blind.

## Facts you must not infer

- Do not invent enum values (sizes, genders, tiers) — read them from `get_app`.
- Do not promise a specific model appearance the presets do not offer.
- Only SUCCESS runs consume credits.

## When to stop or ask

- No product photo: ask for one.
- Ambiguous jewelry type: ask ("is this a bracelet or a bangle-style watch?").
- Insufficient credits: report the balance via `get_balance`.
