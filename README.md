# imagegen

A Pi skill for making and editing images with GPT image generation models.

Use it when you want a real bitmap image: a hero image, product shot, mockup, texture, sprite, cutout, or an edit to an existing picture.

The skill keeps the everyday instructions short. The longer notes live in `references/` so the agent only reads them when something actually needs them.

## Install

This skill requires the `pi-better-skills` extension for compatibility:

```bash
pi install git:https://github.com/edxeth/pi-better-skills
```

Then install the skill:

```bash
npx skills add edxeth/imagegen
```

To repair or upgrade an existing Pi installation non-interactively:

```bash
npx skills add edxeth/imagegen \
  --skill imagegen \
  --agent pi \
  --global \
  --yes
```

The repository keeps `SKILL.md`, `scripts/`, and `references/` together under `imagegen/`. This ensures the skills CLI installs the complete bundle rather than only `SKILL.md`.

## Setup

Add this to your shell config, for example `~/.zshrc`:

```zsh
export PI_IMAGEGEN_BASE_URL='http://localhost:8317/v1'
export PI_IMAGEGEN_API_KEY='dummy'
export PI_IMAGEGEN_MODEL='gpt-image-2'
export PI_IMAGEGEN_OUTPUT_DIR="$HOME/.pi/generated_images"
```

Then reload your shell:

```bash
source ~/.zshrc
```

## Quick start

From the repository root:

```bash
python imagegen/scripts/image_gen.py generate \
  --prompt "A tiny red cube on a plain white background, no text"
```

Save into a project:

```bash
python imagegen/scripts/image_gen.py generate \
  --prompt "A clean product photo of a matte ceramic coffee mug, no text" \
  --out public/images/mug-hero.png
```

Edit an image:

```bash
python imagegen/scripts/image_gen.py edit \
  --image input.png \
  --prompt "Replace only the background with warm sunset light; keep the subject unchanged"
```

From an installed skill directory, the equivalent path remains `scripts/image_gen.py`.
