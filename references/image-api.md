# OpenAI-compatible Image API Notes

This skill targets OpenAI-compatible image endpoints through `scripts/image_gen.py`.

The local proxy is expected to expose:

```text
GET  /v1/models
POST /v1/images/generations
POST /v1/images/edits
```

## Endpoint configuration

Endpoint/auth defaults and precedence live in `references/troubleshooting.md`.

## Parameters

Common parameters:

| CLI option | API field | Notes |
|---|---|---|
| `--model` | `model` | Default `gpt-image-2`. |
| `--prompt` / `--prompt-file` | `prompt` | Required for generate/edit. |
| `--n` | `n` | 1-10. |
| `--size` | `size` | `1024x1024`, `1536x1024`, `1024x1536`, or `auto`. |
| `--quality` | `quality` | `low`, `medium`, `high`, or `auto`. |
| `--background` | `background` | `transparent`, `opaque`, or `auto`; transparent requires PNG or WebP. |
| `--output-format` | `output_format` | `png`, `jpeg`, or `webp`; default `png`. |
| `--output-compression` | `output_compression` | 0-100 when supported. |
| `--moderation` | `moderation` | Passed through when set. |

Edit-only parameters:

| CLI option | API field | Notes |
|---|---|---|
| `--image` | multipart `image` | Repeat for multiple input images. |
| `--mask` | multipart `mask` | PNG mask with alpha channel when supported. |
| `--input-fidelity` | `input_fidelity` | `low` or `high` when supported. |

## Response handling

The CLI accepts either response shape:

```json
{"data":[{"b64_json":"..."}]}
```

or:

```json
{"data":[{"url":"https://..."}]}
```

It writes decoded image bytes to the resolved output path and prints the path.

If the endpoint returns `revised_prompt`, the CLI prints it to stderr for traceability.

## Direct curl tests

Direct generation/edit curl tests live in `references/troubleshooting.md`.

## Invariants for safe edits

Edits drift unless the prompt states the containment boundary. Repeat the invariant in every edit prompt:

```text
Change only <target region/object>. Keep <identity/pose/layout/lighting/text/edges> unchanged. No extra objects. No watermark.
```

For project assets, keep the original file untouched and write a sibling output such as `hero-edited.png` or `product-background-v2.png`.
