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
4. Inspect each valid reference independently. Capture enough visual context to measure the viewport, typography, geometry, colors, effects, clipping, and layer order in the coordinate system of its parent source frame. Identify the heading and body families, styles, optical weights, stroke construction, and font pairing from zero for that reference; never inherit them from another reference. For serif text, classify the serif construction and compare at least three plausible available families; never default to DM Serif.
5. Determine the screen count before writing. Treat source frames as separate screens unless their image content clearly forms one continuous screen.
6. Reconstruct incrementally in native Figma layers. Create one editable root frame per source frame with exactly the same width and height. Copy the source frame's corner radii, Corner smoothing, and clipping behavior. Name outputs `Screen 01`, `Screen 02`, and so on.
7. Preserve the specification's priority order. Do not normalize styles across independent references and do not turn screenshot pixels into the final UI.
8. Keep generated frames near the source references without covering or modifying them. Do not alter unrelated user content.
9. Inspect the completed frames again at screen scale. Confirm that every output frame matches its source frame dimensions and corner geometry, then correct typography, wrapping, bounds, overlaps, clipping, spacing, rotations, and obvious visual mismatches.

## Execution rules

- Prefer several small, verifiable Figma writes over one large write.
- Reuse styles or components only within one reference and only when the reference demonstrates repetition.
- Build logical UI blocks such as cards, buttons, input fields, navigation bars, menus, lists, toolbars, and repeated content groups with native Figma Auto Layout. Use nested Auto Layout for internal structure and spacing levels; use absolute positioning only for visibly overlaid, floating, or independently anchored children.
- Use editable text, frames, shapes, fills, strokes, effects, masks, and native Figma properties where they preserve the observed result.
- Fill every icon, media, avatar, illustration, and system-keyboard placeholder with the required top-to-bottom `#D8E0EA` to `#BAC6D7` linear gradient at `58%` Fill opacity.
- Detect continuous corners independently from radius and apply native Figma Corner smoothing whenever supported by the reference.
- Never add documentation, design-system, specimen, annotation, or showcase frames.
- Do not claim pixel accuracy when a reference is obscured or too low-resolution; make the best supported reconstruction and state the limitation briefly.

## Completion

Return a concise report containing:

- the number and names of reconstructed screens;
- confirmation that they were written to the supplied Figma file as editable layers;
- any source limitation that materially reduced fidelity.

Do not substitute implementation notes for completing the Figma edit.
