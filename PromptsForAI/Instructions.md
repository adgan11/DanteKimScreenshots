# Trial Task Context + Execution Plan (Godot 4 Compatibility, Animal Crossing Cozy Look)

## 0) Why this document exists
This is the full context and a step-by-step, production-style plan to complete the client’s 24-hour $40 trial task for a **Godot 3D Technical Artist / Head of 3D Design** role.  
This document is written so it can be pasted into Codex as the single source of truth for what to do next.

---

## 1) Client’s goal (what they want)
### Visual target
Recreate the **cozy, premium, Animal Crossing-style** interior look. The reference is the Animal Crossing library scene they shared. The client explicitly wants the “cozy vibe,” not a flat/clay/mockup look.

### Job requirements (hard constraints)
1. Must have strong **Godot Engine** experience.
2. Must use **Godot 4 Compatibility renderer** (NOT Forward+, NOT Mobile) for engineering reasons.
3. Must have **GLSL shader** experience.
4. Fast communication.
5. Bonus: played Animal Crossing.
6. “Taste” and attention to detail matter as much as technical correctness.
7. Ability to follow direction that is not super specific.

### Trial task definition (from the chat)
- You already have the Drive files and the Notion brief.
- Deliver a **Godot scene** showing “however much you feel comfortable doing over the next 24 hours,” within the $40 trial budget.
- They will judge: **speed**, **ability to interpret direction**, and **taste**.

---

## 2) Current project state (what we have right now)
### Scene access
- We have access to a complete library scene that visually matches the client’s screenshot layout:
  - Bookshelves on both sides, back wall shelves, tables and chairs, desk/counter at the top, lamps on tables, tall window walls.
- Scene appears to be `library_4.tscn` (or similar).
- Godot version shown: **Godot 4.5.1 stable**.

### Node/scene structure observed
- Root: `Library4`
- Meshes: many imported meshes named like `Plane`, `Table`, `Shelf_001..Shelf_008`, etc.
- Lighting nodes present:
  - `WorldEnvironment`
  - Multiple `DirectionalLight3D` (including `DirectionalLight3D3-ex`)
  - Multiple `SpotLight3D`
  - Multiple `OmniLight3D`
  - Multiple lamp lights (`Lamp_Light_1`, etc.)
- There is also a `CanvasLayer` with a `ColorRect` (likely used as a post overlay tint/vignette or UI filter).

### Critical mismatch with client requirement
- The editor status bar indicates the project is running in **Forward Mobile** currently (not Compatibility).
- This must be corrected first. It is a strict requirement.

---

## 3) What is missing (why it does not feel like Animal Crossing yet)
This is the “gap” between the current look and the target.

### A) Renderer mismatch (must fix)
- Client requires **Compatibility** renderer.
- Current state appears to be **Forward Mobile** in the screenshot.
- If we deliver improvements on the wrong renderer, we lose by default.

### B) Lighting quality and mood (main “cozy” gap)
What Animal Crossing has:
- Warm, soft, controlled lighting.
- Soft bounce feel (global illumination look).
- Lamps contribute believable “pools” of warm light.
- Shadows are soft, never harsh, never muddy.
- Values are controlled: readable, not flat, not overly contrasty.

What the current scene looks like:
- Too uniform/flat in some areas.
- Light feels “game engine default,” not art-directed.
- Lamps do not convincingly shape the room mood.
- Windows look like textures but do not contribute to lighting.

### C) Material response (the “clay/mockup” feeling)
What Animal Crossing has:
- Surfaces that feel toy-like and premium: soft highlights, controlled roughness, subtle variation.
- Strong edge definition (bevels) so objects catch light nicely.
- Ambient occlusion / contact shading that grounds props.

What current scene likely lacks:
- Roughness/specular tuning (wood may look too flat or too plastic).
- Edge definition (bevel effect) is insufficient.
- Contact shadows/AO is weak or missing, so objects float.

### D) Post processing and color management (often the secret sauce)
What Animal Crossing has:
- Cohesive color grade.
- Slight bloom/glow (subtle, not “neon”).
- Controlled exposure and contrast.
- Mild vignette can help focus.

Current scene:
- Either no coherent grading, or grading is not tuned for cozy warmth.
- If ColorRect overlay exists, it may be too blunt or not consistent.

### E) “Taste” details (small wins that separate top candidates)
Animal Crossing environments feel elite because of:
- Consistent palette and value grouping.
- Variation where it matters (books, small props), but still clean.
- Intentional composition and focal hierarchy.

Current scene:
- Looks like assets placed correctly, but not fully art-directed to a “premium cozy” finish.

---

## 4) Priority plan to win (what to do and in what order)
The win condition is a strong “before/after” in **Compatibility** with clear improvements in mood, materials, and polish.

## Phase 1 (First 30–60 minutes): Lock constraints + baseline
**Goal:** eliminate disqualification risks and create a measurable baseline.

1. Switch renderer to **Compatibility**:
   - Godot: Project Settings → Rendering → Renderer → Rendering Method = Compatibility
   - Restart editor if needed.
2. Confirm scene renders correctly in Compatibility:
   - Verify shaders compile.
   - Verify textures/materials render.
   - Verify lights still function.
3. Lock the camera:
   - Keep 1 primary camera angle identical for all comparisons.
4. Create baseline outputs:
   - Screenshot `BEFORE_01.png` (same camera, same resolution).
   - Optional: 10–20 sec slow camera pan recording.

**Checkpoint:** We now meet the client’s main engine constraint and have a baseline to compare.

---

## Phase 2 (1–4 hours): Lighting and Environment (largest visible impact)
**Goal:** deliver “cozy mood” quickly.

### 2.1 Clean up lighting rig (reduce noise)
Current scene has many lights (directional + multiple spots + omnis + lamp lights). Too many lights often create flatness and unpredictable results.

Steps:
1. Identify what each light is doing:
   - Temporarily toggle lights on/off to understand contribution.
2. Reduce to an intentional rig:
   - One primary directional (very soft influence) OR none, depending on desired interior mood.
   - Practical lamps as key mood lights.
   - A gentle fill light if needed (low intensity).
   - Avoid stacking multiple directionals unless they serve different controlled purposes.

### 2.2 WorldEnvironment tuning (Compatibility-friendly)
In `WorldEnvironment`:
- Set a warm ambient tone (subtle).
- Ensure tonemapping/exposure is stable and not washing the scene.
- Add subtle bloom/glow only if supported and it enhances warmth.

### 2.3 Make table lamps “real”
Each table lamp should create:
- A warm pool of light on the table surface.
- Gentle spill around the table/chairs.
- A soft falloff.

Implementation approach:
- Ensure lamp light sources are positioned correctly near lamp geometry.
- Use soft shadows if performance allows (or fake softness with multiple low-intensity lights).
- Balance lamp intensity so they shape the room but do not clip highlights.

### 2.4 Window contribution (fake bounce)
The windows are a major visual element. Even if they are not actual emitters, we should fake their effect:
- Add a very subtle cool or neutral fill from the window direction (low intensity).
- This creates color contrast: warm lamps vs mild cool window fill.

**Checkpoint:** Render `AFTER_LIGHTING_01.png` with noticeable mood improvement.

---

## Phase 3 (4–10 hours): Materials and “clay-to-cozy” conversion
**Goal:** remove clay/mockup feel by fixing the top 3 most visible surfaces.

### Target materials (highest ROI)
1. Floor material (large surface controls the entire room mood)
2. Desk/table wood material
3. Shelf wood + book materials (dominant in composition)

### What to change
1. Roughness/specular tuning:
   - Wood should have soft highlights, not mirror-like, not dead-flat.
   - Use subtle roughness variation if textures allow.
2. Edge definition:
   - Best: bevel geometry or baked normal bevels.
   - If geometry cannot be changed, create a shader-based edge highlight (very subtle).
3. Contact shading (AO feel):
   - Add baked AO maps if available.
   - If not, strengthen contact shadows with lighting placement and subtle AO approach.

**Checkpoint:** Render `AFTER_MATERIALS_CLOSEUP_01.png` and `AFTER_MATERIALS_WIDE_01.png`.

---

## Phase 4 (10–16 hours): Stylized shader pass (GLSL) for “Animal Crossing softness”
**Goal:** demonstrate GLSL skill and improve style without making it look like a shader demo.

### Shader features to implement (lightweight, tasteful)
- Wrapped diffuse (softens shading transitions).
- Soft specular response (toy-like, gentle).
- Subtle rim light (helps silhouettes and depth).
- Optional: very mild color variation/noise to avoid sterile gradients.

Rules:
- Keep it subtle. Animal Crossing is clean.
- Avoid heavy outlines unless reference clearly supports them.
- Ensure it works in Compatibility.

**Checkpoint:** Render `AFTER_SHADER_01.png` showing improved depth and softness.

---

## Phase 5 (16–22 hours): Polish and art direction (taste signal)
**Goal:** the scene should feel “finished” and intentionally art-directed.

Polish checklist:
- Confirm focal hierarchy (desk area + tables) reads clearly.
- Reduce monotony:
  - Slight color variation in books (if possible).
  - Subtle decals or texture breakup only where needed.
- Add subtle vignette (optional) via Environment or CanvasLayer ColorRect:
  - If using ColorRect overlay, it must be extremely subtle and not crush values.

**Checkpoint:** final beauty screenshot(s) plus a short camera pan.

---

## Phase 6 (Last 1–2 hours): Packaging and submission
**Goal:** present like a top candidate.

Deliverables:
1. `BEFORE_01.png` and `AFTER_FINAL_01.png` (same camera).
2. 1–2 close-ups (materials and lamp lighting).
3. Short 20–60 sec camera pan video.
4. A short changelog message:
   - Renderer: switched to Compatibility.
   - Lighting: environment tuned, lamp pools, window fill.
   - Materials: roughness/specular improvements, edge definition, AO/contact shading.
   - Shader: stylized lighting improvements.

---

## 5) Concrete “Do this now” checklist (execution order)
1. Switch renderer to **Compatibility** and verify scene stability.
2. Save baseline screenshot (BEFORE).
3. Lighting pass:
   - Clean light count.
   - Tune WorldEnvironment.
   - Make lamps create warm pools.
   - Add subtle window fill.
4. Material pass:
   - Floor first.
   - Tables second.
   - Shelves/books third.
5. GLSL pass:
   - Wrapped diffuse + soft spec + subtle rim.
6. Final grade/polish:
   - Check exposure, saturation, contrast.
   - Optional subtle vignette.
7. Output:
   - Before/after.
   - Close-ups.
   - Short pan video.
   - Changelog text.

---

## 6) Quality bar (what “good” looks like)
If we did it right:
- The room feels warmer and softer but still readable.
- Lamps shape the mood and create cozy pools.
- Wood feels premium: soft highlights, not plastic, not dead.
- Objects feel grounded (contact shading).
- Overall look feels like a deliberate art direction, not “assets placed in engine.”
- It runs in **Compatibility** renderer.

---

## 7) Risks and how to avoid losing
- Risk: working on the wrong renderer (Mobile/Forward+)  
  Fix: switch to Compatibility immediately and keep it that way.
- Risk: too many lights flattening the scene  
  Fix: simplify lighting rig and art-direct it.
- Risk: over-stylized shader  
  Fix: keep stylization subtle and “clean.”
- Risk: no clear before/after proof  
  Fix: lock camera and deliver direct comparisons.

---

## 8) Quick client update template (send early)
Use this as your first update after you make real progress:

“I switched the project to the Compatibility renderer and captured a baseline render. I am starting with an environment + lighting pass to lock the cozy mood (warm ambient, controlled exposure, and lamps that create real light pools). Next I will tune the key materials (floor, tables, shelves) and then add a subtle stylized GLSL lighting pass for the Animal Crossing softness. I will share before/after renders once the lighting pass is complete.”

---
End of file.
