---
name: "imagegen"
description: "Generate or edit raster images from Pi through the bundled OpenAI-compatible image CLI. Use for bitmap visuals: photos, illustrations, textures, sprites, product mockups, UI mockups, transparent-background cutouts, or raster image edits. Do not use when SVG/vector/code-native assets are the better fit."
---

# Image Generation Skill for Pi

Use the bundled CLI for raster image generation and editing:

```bash
python "$PI_SKILL_DIR/scripts/image_gen.py" ...
```

## Core contract

- The CLI is the implementation boundary: `scripts/image_gen.py`.
- Let the CLI/env choose endpoint, model, auth, and default preview output path.
- For setup, env precedence, current defaults, curl tests, or failures, read `references/troubleshooting.md` lazily.
- For command details beyond the minimal examples below, read `references/cli.md` lazily.

## Safety rules

- Never ask the user to paste secrets in chat; ask them to set env vars locally.
- Never overwrite an existing file unless the user asked for replacement or `--force` is intentional.
- Preview-only images may stay in the configured generated-images directory.
- Project-referenced assets must be saved inside the project via `--out` or `--out-dir` and then wired into consuming code.
- Edits must preserve invariants aggressively and write a new file by default.

## When to use

Use this for new raster images, raster edits, variants, and batches.

Do not use it for SVG/vector/code-native assets, deterministic diagrams, or editable native source assets.

## Decision tree

1. **Intent:** generate or edit?
   - Change an existing image while preserving parts of it → `edit`.
   - Use supplied images only as references → `generate`, and label their roles in the prompt.
   - No images → `generate`.
2. **Ownership:** preview-only or project-bound?
   - Preview → omit `--out` unless the user requested a path.
   - Project asset → use `--out` or `--out-dir` inside the project.
3. **Scale:** one prompt, variants, or batch?
   - One image → `generate` / `edit`.
   - Variants → `generate --n <count>`.
   - Many prompts → `generate-batch`.

## Workflow

1. Confirm the task needs a raster image.
2. Build a short structured prompt using only useful fields from the schema below.
3. Run the CLI; use `--dry-run` first for complex commands.
4. Check output path and file metadata; visually inspect when possible.
5. Iterate with one targeted change if needed.
6. Report final path, model, endpoint, and final prompt.

## Minimal examples

Generate preview:

```bash
python "$PI_SKILL_DIR/scripts/image_gen.py" generate \
  --prompt "A tiny red cube on a plain white background, no text"
```

Generate project asset:

```bash
python "$PI_SKILL_DIR/scripts/image_gen.py" generate \
  --prompt "A minimal hero image of a ceramic coffee mug" \
  --use-case product-mockup \
  --asset-type "landing page hero" \
  --constraints "no logos, no text, no watermark" \
  --out "public/images/mug-hero.png"
```

Edit:

```bash
python "$PI_SKILL_DIR/scripts/image_gen.py" edit \
  --image "input.png" \
  --prompt "Replace only the background with warm sunset light" \
  --constraints "change only the background; keep the subject and edges unchanged; no text; no watermark"
```

## Prompting rules

- Preserve detailed user prompts; only normalize them.
- For generic prompts, add only details that materially improve output quality.
- Allowed additions: composition, framing, intended use, polish level, practical layout guidance, and reasonable scene concreteness.
- Do not add unrelated objects, characters, brands, slogans, palettes, story beats, or arbitrary left/right placement.
- For edits, always state the invariant: `change only X; keep Y unchanged`.

## Prompt schema

Use only lines that help:

```text
Use case: <taxonomy slug>
Asset type: <where the asset will be used>
Primary request: <user's main prompt>
Input images: <Image 1: role; Image 2: role> (optional)
Scene/backdrop: <environment>
Subject: <main subject>
Style/medium: <photo/illustration/3D/etc>
Composition/framing: <wide/close/top-down; placement>
Lighting/mood: <lighting + mood>
Color palette: <palette notes>
Materials/textures: <surface details>
Text (verbatim): "<exact text>"
Constraints: <must keep/must avoid>
Avoid: <negative constraints>
```

Taxonomy and copy/paste prompt recipes live in `references/prompting.md` and `references/sample-prompts.md`; read them only when needed.

## Lazy reference map

- `references/troubleshooting.md`: setup, env vars, defaults, direct curl tests, failure recovery.
- `references/cli.md`: command usage and batch/advanced examples.
- `references/image-api.md`: API parameters and endpoint behavior.
- `references/prompting.md`: prompting principles and taxonomy.
- `references/sample-prompts.md`: copy/paste prompt recipes.
