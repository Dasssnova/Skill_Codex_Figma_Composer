---
name: figma-compozer
description: Reconstruct mobile UI screenshots or visual references found in Figma as pixel-accurate, structured, editable Figma screens. Use when a user provides a Figma file or node link and asks Codex to analyze layers named exactly `Composition__reference`, recreate their visible mobile interfaces, reproduce typography and geometry, or return editable 393 x 852 UI frames in the same Figma file.
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
2. Find only layers whose name is exactly `Composition__reference`. Ignore all near matches and all other visual material as reconstruction evidence.
3. Inspect each valid reference independently. Capture enough visual context to measure the actual viewport, typography, geometry, colors, effects, clipping, and layer order. Identify the heading and body families, styles, optical weights, stroke construction, and font pairing from zero for that reference; never inherit them from another reference. For serif text, classify the serif construction and compare at least three plausible available families; never default to DM Serif.
4. Determine the screen count before writing. Treat references as separate screens unless they clearly form one continuous screen.
5. Reconstruct incrementally in native Figma layers. Create one editable `393 x 852` root frame per screen and name frames `Screen 01`, `Screen 02`, and so on.
6. Preserve the specification's priority order. Do not normalize styles across independent references and do not turn screenshot pixels into the final UI.
7. Keep generated frames near the source references without covering or modifying them. Do not alter unrelated user content.
8. Inspect the completed frames again at screen scale. Correct typography, wrapping, bounds, overlaps, clipping, spacing, rotations, and obvious visual mismatches.

## Execution rules

- Prefer several small, verifiable Figma writes over one large write.
- Reuse styles or components only within one reference and only when the reference demonstrates repetition.
- Use editable text, frames, shapes, fills, strokes, effects, masks, and auto layout where they preserve the observed result.
- Use neutral wireframes for icons and media exactly where required by the reconstruction specification.
- Never add documentation, design-system, specimen, annotation, or showcase frames.
- Do not claim pixel accuracy when a reference is obscured or too low-resolution; make the best supported reconstruction and state the limitation briefly.

## Completion

Return a concise report containing:

- the number and names of reconstructed screens;
- confirmation that they were written to the supplied Figma file as editable layers;
- any source limitation that materially reduced fidelity.

Do not substitute implementation notes for completing the Figma edit.
