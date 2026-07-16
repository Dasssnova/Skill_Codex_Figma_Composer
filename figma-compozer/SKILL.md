---
name: figma-compozer
description: Reconstruct UI screenshots or visual references placed inside Figma frames as pixel-accurate, structured, editable Figma screens. Use when a user provides a Figma file or node link and asks Codex to find reference images regardless of layer or frame names, recreate their visible interfaces, preserve each source frame's dimensions and corner geometry, or reproduce typography and layout as editable layers in the same Figma file.
---

# Reconstruct Mobile UI in Figma

Rebuild every valid reference directly in the supplied Figma file. Treat the reference images as evidence, not as assets to trace into the output.

## Required instructions

1. Load and follow the `figma-use` skill before any `use_figma` call.
2. Load `figma-generate-design` as complementary guidance for composed screen construction.
3. Read [references/reconstruction-spec.md](references/reconstruction-spec.md) in full before inspecting or editing the Figma file. It is the authoritative reconstruction contract; follow it even when it is more restrictive than general Figma guidance.
4. Use the Figma link from the user's current request. If no link or uniquely identifiable open file is available, ask for the Figma link and do not guess the target.

## Workflow

1. Open the target file and inspect its page and layer structure.
2. Within the user-supplied selection, linked node, or clearly designated input area, find image layers that serve as complete UI references inside parent Figma frames. Ignore all layer and frame names when deciding validity; names may use any wording, abbreviation, language, or spelling.
3. Treat each parent frame containing a complete UI reference image as one independent source frame. Record its exact width, height, clipping, four corner radii, and Corner smoothing before analyzing the image.
4. Inspect each valid reference independently. Capture enough visual context to measure the viewport, typography, geometry, colors, effects, clipping, and layer order in the coordinate system of its parent source frame. Analyze the complete color field before classifying a surface as solid or linear: test for off-center radial or elliptical blooms, angular or diamond transitions, vignettes, layered gradients, translucency, and blur. Identify the heading and body families, styles, optical weights, stroke construction, and font pairing from zero for that reference; never inherit them from another reference. For serif text, classify the serif construction and compare at least three plausible available families; never default to DM Serif.
5. Determine the screen count before writing. Treat source frames as separate screens unless their image content clearly forms one continuous screen. Establish the processing queue, but do not reconstruct several independent references in one batch.
6. Process one source frame to completion before writing the next. For the active reference, reconstruct incrementally in native Figma layers: create one editable root frame with exactly the source frame's width and height, copy its corner radii, Corner smoothing, and clipping behavior, then build and verify its logical UI blocks. Name outputs `Screen 01`, `Screen 02`, and so on.
7. Preserve the specification's priority order. Do not normalize styles across independent references and do not turn screenshot pixels into the final UI.
8. Keep generated frames near the source references without covering or modifying them. Do not alter unrelated user content.
9. Inspect the active completed frame again at screen scale. Confirm that it matches its source frame dimensions and corner geometry, then correct typography, wrapping, bounds, overlaps, clipping, spacing, rotations, gradients, opacity, and obvious visual mismatches. Move to the next reference only after the current screen passes this check.

## Execution rules

- Prefer several small, verifiable Figma writes over one large write. Keep each `use_figma` call to at most 10 logical operations; one logical operation is creating or mutating a node, setting its relevant properties, and placing it in the hierarchy.
- Build the active screen top-down: root and major-region skeleton first, then one logical block such as a header, card group, form, list, toolbar, or navigation region per write. Return every created or mutated node ID from each call.
- Capture and inspect a screenshot after each completed major block. Correct geometry, clipping, typography, gradients, transparency, and overlaps before building the next block; do not accumulate known mismatches for a final cleanup pass.
- Process independent references sequentially for quality. A request may contain many source frames, but analyze, write, validate, and finish `Screen NN` before starting `Screen NN+1`.
- Reuse styles or components only within one reference and only when the reference demonstrates repetition.
- Build logical UI blocks such as cards, buttons, input fields, navigation bars, menus, lists, toolbars, and repeated content groups with native Figma Auto Layout. Use nested Auto Layout for internal structure and spacing levels; use absolute positioning only for visibly overlaid, floating, or independently anchored children.
- Use editable text, frames, shapes, fills, strokes, effects, masks, and native Figma properties where they preserve the observed result.
- Separate every icon vector from its icon background, button container, and surrounding surface. Reconstruct each background from the reference, then use only an editable vector icon from enabled Figma libraries, local file components, or Figma Community with visual style taking priority over exact symbol meaning. Never create an icon as a text layer, Unicode character, emoji, icon-font glyph, symbol-font glyph, or other font character.
- For photographs and raster media, use any image files from the skill's `assets/` directory without analyzing or matching their subject, composition, colors, orientation, or aspect ratio. Distribute different available files across media containers and avoid repeating one asset while unused alternatives remain. Use the shared placeholder gradient only when `assets/` contains no usable image file. The system-keyboard placeholder always uses the required top-to-bottom `#D8E0EA` to `#BAC6D7` gradient at `58%` Fill opacity.
- Detect continuous corners independently from radius and apply native Figma Corner smoothing whenever supported by the reference.
- Detect translucent layers independently from light colors, gradients, blur, and shadows. Before accepting an opaque color, compare the object with the background directly beside and beneath it and test whether the visible color is their composite. When object and background belong to a close hue family, assign a 90% prior probability to translucency and disprove it before choosing opaque. If evidence remains ambiguous, prefer a translucent native Figma Fill or layer over a background-mixed solid RGB color. Reproduce transparency with native Figma Opacity controls at the narrowest correct level.
- Reconstruct complex color fields as editable native Figma paints and Effects. Prefer one correctly transformed Radial, Angular, or Diamond Fill over a Linear approximation; when the reference contains several independent color blooms, stack the minimum necessary native gradient Fills in visible back-to-front order. Use Background blur, Layer blur, shadows, and paint or layer opacity only for the distinct phenomena they represent. Never rasterize a gradient or bake blur into an image.
- Never add documentation, design-system, specimen, annotation, or showcase frames.
- Do not claim pixel accuracy when a reference is obscured or too low-resolution; make the best supported reconstruction and state the limitation briefly.

## Completion

Return a concise report containing:

- the number and names of reconstructed screens;
- confirmation that they were written to the supplied Figma file as editable layers;
- any source limitation that materially reduced fidelity.

Do not substitute implementation notes for completing the Figma edit.
