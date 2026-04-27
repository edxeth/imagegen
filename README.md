# imagegen

A Pi skill for making and editing images with GPT image generation models.

Use it when you want a real bitmap image: a hero image, product shot, mockup, texture, sprite, cutout, or an edit to an existing picture.

The skill keeps the everyday instructions short. The longer notes live in `references/` so the agent only reads them when something actually needs them.

## Install

```bash
npx skills add edxeth/imagegen
```

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

```bash
python scripts/image_gen.py generate \
  --prompt "A tiny red cube on a plain white background, no text"
```

Save into a project:

```bash
python scripts/image_gen.py generate \
  --prompt "A clean product photo of a matte ceramic coffee mug, no text" \
  --out public/images/mug-hero.png
```

Edit an image:

```bash
python scripts/image_gen.py edit \
  --image input.png \
  --prompt "Replace only the background with warm sunset light; keep the subject unchanged"
```
