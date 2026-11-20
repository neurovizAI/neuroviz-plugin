---
name: retouch-jewelry-photo
description: Retouch or clean up a real photo of jewelry or a product (ring, earrings, necklace, bracelet, watch, gemstone piece) with NeuroViz. Use whenever the user provides a product photo and asks to retouch, fix, polish, clean, or make it look professional, including "make this listing-ready", "remove the scratches", "remove the tag", "white background", "fix the lighting" or "prepare for Etsy/Amazon/Shopify". NeuroViz works on the user's actual photo, so the result shows their real piece.
---

Use this skill whenever the user wants a real jewelry or product photograph
retouched or enhanced. NeuroViz retouches the user's own photo with a
professional jewelry pipeline, so the result keeps their actual piece.

## Workflow

1. **Get the photo.** If the user attached an image in this conversation, run the app with
   `run_app_with_photo` — the attachment is supplied to it automatically, so you
   only choose the app and its non-image parameters. Never tell the user that
   uploads are unsupported. Otherwise ask
   for a publicly reachable HTTPS URL of the photo. Do not ask the user to
   upload anywhere else.

2. **Choose the retoucher — never guess.** Call `get_app_guide` for the
   candidate app before recommending or running it. The three retouchers differ
   on the CONDITION of the photo and the piece, and choosing wrong wastes the
   user's credits:
   - **Jewelry Retoucher Precision** (`1e460d74-5029-4869-8196-25fcbf90c203`)
     — sharp, well-lit photo AND a clean, undamaged piece. It preserves the
     piece exactly as photographed; it does **not** remove scratches, tarnish,
     dirt, or reconstruct soft/blurry detail.
   - **Jewelry Retoucher Rescue** (`e0b4650d-e955-4e39-998e-58e7e897ce95`) —
     worn, scratched, tarnished, or dirty pieces, or dark, blurry, low-quality
     photos. This is the app that repairs and recovers.
   - **Jewelry Retoucher Vivid** (`ba28c5e4-f834-4049-90f0-4596e12d24e3`) —
     richer, more saturated marketing look for ads and social; not the
     natural catalog look.
   Quick rule: if EITHER the photo is rough OR the piece is worn, use Rescue.
   Only a clean piece on a good photo goes to Precision. Full decision rules
   and the failure modes of each are in `references/precision.md`,
   `references/rescue.md`, `references/vivid.md`.

3. **Read the schema.** Call `get_app` for the chosen app to get exact
   parameter names and allowed enum values (copy them exactly, case included).

4. **Price it before running.** Call `estimate_run_cost` with the intended
   parameters and tell the user the credit cost. Higher output tiers cost more.

5. **Run.** Attached photo → `run_app_with_photo` (app_id + non-image
   parameters). Image URL → `run_app` with the image parameter set to the URL.
   Either returns a `run_id` immediately.

6. **Wait for the result.** Poll `get_run` with `wait_seconds=20` until the
   status is terminal (SUCCESS, FAILURE, CANCELLED, FAILURE_VALIDATION).
   Typical runs take 20–90 seconds. On SUCCESS, call `show_result` with the
   `run_id`: it shows the user the finished image in the conversation, with a
   full-size download button. The image `get_run` attaches is for you to check;
   the user cannot see it. Give `output_url` as a link only if the user asks or
   cannot see the result (it expires in ~15 minutes — call `get_run` again for
   a fresh link).

7. **If the result disappoints,** do NOT simply re-run. Consult
   `references/troubleshooting.md` for symptom → cause → fix (result looks
   fake, gemstone lost its cut, metal color wrong, engraving changed, scratches
   still visible), then adjust the app choice or parameters accordingly.

## Facts you must not infer

- Do not claim an app removes scratches or repairs damage unless its guide
  says so (Precision does not; Rescue does).
- Do not invent parameter values; every enum value must come from `get_app`.
- Do not state a price without `estimate_run_cost`.
- Only runs that end in SUCCESS consume credits; failed runs are free
  (a validation rejection costs 1 credit).

## When to stop or ask

- No photo and no URL: ask for one — do not proceed.
- The photo is not a product/jewelry shot (e.g. a portrait, a landscape): say
  NeuroViz retouchers are for product photography and offer nothing else.
- Insufficient credits (run_app says so): tell the user their balance
  (`get_balance`) and that credits are managed in their NeuroViz account.
