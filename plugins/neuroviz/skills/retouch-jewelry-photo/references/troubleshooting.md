# NeuroViz Troubleshooting — Agent Guide

_Global decision rules and failure diagnosis that apply across the NeuroViz catalog. Every image input is a publicly reachable HTTPS URL. Parameter names and enum values below are copied verbatim from each app's schema — copy them exactly, case included. Where behavior described here conflicts with an app's own guide or schema, the schema wins._

## 1. Choosing the right app (decision rules)

Pick by goal, not by app name.

**Retouch a jewelry photo** (clean, professional result on white/solid/transparent background):
- Difficult source — blurry, dark, low-resolution, on a stand — or a piece with scratches, tarnish, oxidation, or dirt: **Jewelry Retoucher Rescue**
- Sharp photo of a clean piece, maximum faithful detail and highest resolution: **Jewelry Retoucher Precision**
- Richer, more saturated look with deeper shadows and stronger contrast, for marketing/editorial: **Jewelry Retoucher Vivid**
- Any product on a marketplace-standard white or solid-colour background (jewelry and watches included): **Retoucher Pro for Marketplaces**

Quick rule for the three jewelry retouchers: **Precision to preserve, Rescue to recover, Vivid for a bolder look.**

**Put jewelry or a watch on a model** (virtual try-on):
- Rings: **Rings Try On**
- Earrings: **Earrings Try On**; **Earrings Try-On Studio** for precise manual size/placement control
- Necklaces and pendants: **Necklace Try - On**; **Necklace Try-On Studio** for precise manual size/placement control
- Bracelets and bangles: **Bracelet Try-ON**
- Watches: **Watch Try-On**
- Two or three pieces at once: **Jewelry Set Try-On**
- Toe rings, anklets, hair or body jewelry — no dedicated try-on exists: **Product + Model Studio**. In its `user_prompt` parameter state exactly what the item is and how it is worn, e.g. "this is a toe ring, show it worn on the second toe of a bare foot" or "this is an anklet, show it on a bare ankle". Explaining the item and its placement is what makes this route work.

**Watches specifically:**
- **Watch Try-On** is for complete wrist watches (dress, sport, luxury, smart).
- A bracelet or fitness band with **no visible watch face** goes through **Bracelet Try-ON**, not Watch Try-On.
- **Jewelry Set Try-On** does not include watches — run the watch separately in **Watch Try-On**.
- A strap, case, or dial on its own belongs in a retoucher, not a try-on.
- The retouchers (**Jewelry Retoucher Rescue**, **Jewelry Retoucher Vivid**, **Jewelry Retoucher Precision**) all accept watches and timepieces, and **Jewelry Modifier Pro** can change a watch's appearance.

**Create a styled, creative scene** around a piece: **Creative Jewelry Photographer Vivid** (richer, more saturated, deeper shadows) or **Creative Jewelry Photographer V1** (a different editorial mood).

**Turn a sketch or design concept into a photorealistic image:** **Jewelry Design Visualizer**; **Jewelry Design Visualizer V2** adds transparent-background output (`background_settings`: `"auto"`, `"transparent"`, `"opaque"`). These accept sketches and drawings only — never a real photo.

**Change the piece itself** (metal, stones, finish, shape, background, engraving): **Jewelry Modifier Pro**.

**Make a video:**
- Animate one photo: **Product Video Creator**
- Transition between two frames, or a correct 360 spin from a front and a back photo: **Scene Video Creator**
- The same at up to 4K, with audio available and especially strong lifestyle video with people: **Scene Video Creator 2.0**

**General products, clothing, and utilities** (non-jewelry is fully supported):
- Marketplace-ready white background for any product: **Retoucher Pro for Marketplaces**
- General product editing and scene changes (bags, accessories, cosmetics, home decor, any e-commerce item): **AI Photo Editor Pro – Retouching & Editing V2**
- Creative scenes for non-jewelry products: **Creative Photographer**
- Clothing on a model: **Fashion Model Studio**; **Fashion Studio Advanced** for more control (its `style_reference` image copies pose and environment)
- A product shown with a person: **Product + Model Studio**
- Remove unwanted objects: **Object Remover** (name the objects in `elements_removal`, English only)

The jewelry-specific apps (retouchers, try-ons, **Jewelry Modifier Pro**) are tuned for jewelry and give their best results there; route non-jewelry items through the general-product apps above. Video apps accept any product.

## 2. Result-quality complaints: symptom → why → what to change

### The result looks fake, plastic, or over-processed

**Symptom:** surfaces too smooth, metal with no grain, an airbrushed render-like finish instead of a photograph.

**Why:** retouching enhances detail that is actually present. A low-resolution, soft, or unevenly lit source gives the app too little real detail to work from, so the output comes back smoother and flatter than the real piece. The cause is almost always the source image, not a parameter.

**What to change, in order of impact:**
1. Provide a sharper, larger source image URL — at least 2000 px on the shortest side, fully in focus, piece filling most of the frame. This single change resolves most over-processed results.
2. Even, diffused, neutral lighting — no strong warm or colored cast, no harsh single-source light or deep shadow hiding surface texture.
3. Crop the image tighter before hosting it and pass the cropped version's URL — more pixels on the piece means more true detail.
4. Do not pre-cut the background — pass the photo as shot; background removal and replacement are handled by the app.
5. Match the app to the photo: **Jewelry Retoucher Precision** stays closest to what is in the picture (best when the photo is sharp and the piece clean); **Jewelry Retoucher Rescue** rebuilds more and is the right trade for rough, dark, or blurry sources and worn pieces; **Jewelry Retoucher Vivid** is a deliberate bolder color-and-lighting look, not a lower-quality option.

### The gemstone looks too smooth, too clean, or lost its cut

Two distinct causes with different fixes.

**Cause A — the stone was cleaned up more than wanted.** The `retouch_gemstones` boolean (default `true`) exists on all three current retouchers — **Jewelry Retoucher Precision**, **Jewelry Retoucher Rescue**, and **Jewelry Retoucher Vivid**. With `true` the stone surfaces are cleaned (dust, lint, smudges, fine scratches) and natural inclusions are kept; set `retouch_gemstones: false` to leave the stones exactly as photographed (important when buyers expect the real stone). The result is generated, so a stone can still differ slightly from the source. Set it on the app already in use — no need to switch apps for this.

**Cause B — the facets were not visible enough in the source.** A cut only renders correctly when it is clearly visible: faceting is lost when the photo is soft, the stone is small in the frame, glare washes across the table, or the piece is underexposed. Provide a source shot straight on rather than at a steep angle, diffused light to kill hotspots, sharp focus on the stone, and a tighter crop so the stone occupies more pixels.

**Related:** a stone that came out too dark usually reflects source exposure — brighten the image before hosting it, or route the run through **Jewelry Retoucher Rescue**, which is built to recover dark and difficult images. For an exact stone color, set it explicitly via the `color` custom-colors parameter (see next section) instead of relying on the photo.

### Metal or stone color came out wrong

**Symptom:** the metal tone or a stone's color does not match the real piece.

**Why:** almost always the source photo — colored light, a colored background or nearby surface reflecting into the metal, strong glare on the stone, or the piece too small/soft in the frame for the color to read.

**What to change:**
- Provide a photo with even, neutral (white) lighting and a plain neutral background — no colored walls, cloths, or lamps near the piece.
- Avoid glare directly on stones; a slight angle change removes most hotspots.
- Crop closer before hosting so the piece fills the frame — color is read from more pixels.
- Set color explicitly instead of relying on the photo:
  - The retouchers (**Jewelry Retoucher Precision**, **Jewelry Retoucher Rescue**, **Jewelry Retoucher Vivid**) take a `color` parameter with up to 3 reference colors (metal tone, gemstone, enamel, or any element). The picks only take effect when the instructions parameter references them as "Color #1", "Color #2", "Color #3".
  - **Jewelry Modifier Pro** has a `color` Custom Colors Selector for exact tones alongside its required `user_instructions`.
  - In apps that accept free-text instructions, state the color plainly: "the band is white gold, keep it cool silver-toned".
- Re-run after the change — color fidelity usually locks in once the source reads cleanly or the color is set explicitly.

### Engraved text, hallmarks, or stamps changed

**Symptom:** engraving, a hallmark, a maker's mark, or a stamp comes back moved, restyled, redrawn in a generic font, or altered (e.g. a "14K" becomes something else).

**Why:** the marking was not sharp and legible in the source. Small, soft, or angled markings get treated as surface detail to clean up and are rebuilt. Text is not read the way a person reads it — a marking that cannot be seen clearly will never be reproduced reliably.

**What to change:**
- **Provide a sharp close-up of the marking** — clearly legible, straight on, no glare across it. This is the fix that matters most.
- **Use the most faithful app:** **Jewelry Retoucher Precision** stays closest to what is in the photo rather than rebuilding, so it is the right choice when a hallmark must survive — but it needs a sharp photo of a clean piece. If the piece is worn or dirty, have it cleaned and re-shot rather than switching to an app that rebuilds more.
- **Name the marking in the instructions parameter**, e.g. "keep the 14K hallmark exactly where it is on the inner band, do not move it or change its style."
- **Consider framing that excludes the mark** — if the hallmark is not needed in the listing image, crop it out of the source before hosting and the problem disappears.
- There is no masking or brush mechanism to paint a marking back in. The fix is a better source image plus an explicit instruction, then a re-run.

**Compliance warning to surface:** if a hallmark, karat stamp, or brand mark came back altered, that image must not be used commercially — a listing photo showing a wrong karat or maker's mark misrepresents the item and is a real risk for the seller.

### Dirt, dust, or debris turned into stones or other detail

**Symptom:** the result shows stones, sparkle, or detail the real piece does not have — most often where dirt, dust, polishing compound, or debris sat in a channel, prong seat, or along a rail in the source.

**Why:** retouching enhances what it reads as part of the piece. Small bright specks in a setting look like tiny set stones, so they get rendered as stones instead of removed.

**What to change:**
- **Have the piece physically cleaned before it is photographed** — the single most effective fix (dust, fingerprints, polishing residue, debris in channels and prong seats).
- **Say so in the instructions parameter** — every current retoucher accepts free text: "the marks along the side rail are dirt, not stones — remove them."
- **Provide a sharper, higher-resolution image** — resolution is what separates a dust speck from a set stone.
- **Match the app to the intent:** **Jewelry Retoucher Rescue** is built to clean up worn and dirty pieces — right choice when debris should go. **Jewelry Retoucher Precision** stays faithful to the photo — right when the surface must stay exactly as shot, which also means a dirty piece stays dirty, so clean it first.
- **Inspect the result before it is used.** An image showing stones the piece does not have must not go on a listing — it misrepresents the item.

### Try-on sizing and proportions look wrong

**Symptom:** the piece appears too large or too small on the model.

**Two levels of control:**
1. **The size parameter.** The standard try-on apps — **Rings Try On**, **Earrings Try On**, **Necklace Try - On**, **Bracelet Try-ON**, **Watch Try-On**, **Jewelry Set Try-On** — take a size enum: `"XS (Extra Small)"`, `"S (Small)"`, `"M (Medium)"`, `"L (Large)"`, `"XL (Extra Large)"`. The parameter is `product_size` (in **Jewelry Set Try-On** it is per item: `product_size_1`, `product_size_2`, `product_size_3`). Set the value matching the real piece and re-run — this resolves most sizing issues.
2. **Manual placement.** **Necklace Try-On Studio** and **Earrings Try-On Studio** give full control: their required `frame_background` image encodes the piece's exact size and position (a composite you prepare with the piece placed and scaled on the placement reference), and the output matches the size and placement encoded there. When `product_size` in a standard try-on cannot get proportions right, these two Studio apps are the precise option — rebuild the `frame_background` composite with the correct scale.

**Source rules that also matter:** show the whole piece clearly, straight on, without heavy cropping or extreme perspective, so its proportions read correctly.

## 3. Input preparation rules

**Formats and limits (all apps):**
- Image URLs must resolve to **JPEG, PNG, or WebP**. **TIFF is not supported** — re-encode as JPEG or PNG and host that version instead.
- Minimum **0.15 megapixels** (e.g. 400×400 or 600×250), any aspect ratio up to **8:1**; **2000 px or larger gives the best results**. Masks and cut-outs need at least **32 px** per side. Each image file must be **40 Mb or smaller**.
- If a photo is refused as too small, use the original photo rather than a thumbnail or screenshot. A size refusal is not charged; get_run then shows `error_code` `input_image_too_small`, `input_image_aspect_too_extreme` or `input_crop_too_small`.
- Results come back as JPEG, PNG, or lossless WebP up to 4K (16 megapixels).

**Fix the source before running — it beats regenerating afterward:**
- **Crop** closer when the subject is small in the frame with lots of empty background, then pass the cropped version's URL.
- **Rotate** a sideways or upside-down piece to the correct orientation in the file before hosting it.
- **Do not cut out the background yourself** — background removal and replacement are handled inside the apps.
- Sharp focus, even neutral light, a clean piece, one subject per image, the whole piece in frame.

**Consistent look across several photos of the same piece** (one-photoshoot catalogs):
- Run every angle through the **same app with the same settings** — same resolution/tier, same aspect ratio, same toggles.
- Describe the background **identically, word for word**, in every run's instructions (e.g. "deep navy background" in each one) — identical wording gives the most consistent interpretation.
- Keep the source photos consistent too: similar lighting and distance across angles.
- **Bulletproof route for large catalogs:** output each angle with a transparent background — `bg_mode: "transparent"` on **Jewelry Retoucher Vivid** (also available on **Jewelry Retoucher Precision** and **Jewelry Retoucher Rescue**) — and composite the cut-outs onto one background template yourself.
- To turn multiple angles into one video, use **Scene Video Creator** or **Scene Video Creator 2.0** with two frames of the same piece. For a correct 360-degree spin in **Scene Video Creator 2.0**, pass a front photo as `first_frame`, a back photo as `last_frame`, and set `back_side_image: true`.

**Instructions parameter naming:** most apps call it `user_instructions`, but several differ (`user_prompt` in **Product + Model Studio** and **Product Video Creator**, `custom_instructions` in **Scene Video Creator 2.0** and **Fashion Studio Advanced**, `instructions_setting` in **Necklace Try-On Studio**, `additional_instructions` in **Scene Video Creator**). Always take the exact name from the target app's schema.

## 4. Why a run might not complete

- **Content involving children:** visualizing children's clothing and similar catalog use is legitimate, but safety screening may block submissions involving children, so some of these runs do not complete. Workaround: show the item on its own (flat-lay or hanger) or use an adult reference.
- **Watermark removal:** removing watermarks, logos, or copyright marks is not supported — they often signal someone else's rights. Other unwanted objects can be removed with **Object Remover**.
- **Wrong app for the input:** for example, a real photograph sent to **Jewelry Design Visualizer** (it accepts sketches and drawings only), or a try-on request sent to a retoucher. Re-route per the decision rules in section 1.
- **Very low-quality or unclear source:** the result reflects the input; a sharper source image fixes most of these.
- **Rejected image file:** wrong format (e.g. TIFF) or over the 40 Mb limit — re-encode as JPEG or PNG and pass the new URL.

When a run fails repeatedly after these checks, gather what the goal was and what the source image looks like, then re-route using section 1 before retrying.
