---
name: evolink-cheapest-image
description: Possibly the cheapest AI image generation (~$0.0036/image). Text-to-image via the EvoLink API.
metadata: {"openclaw": {"emoji": "🖼️", "requires": {"env": ["EVOLINK_API_KEY"]}, "primaryEnv": "EVOLINK_API_KEY"}}
---

# EvoLink Cheapest Image

Generate images via the EvoLink z-image-turbo API.

## Run

```bash
python3 {baseDir}/scripts/generate.py --prompt "a cute cat" --size "1:1"
```

Options: `--size` (1:1, 2:3, 3:2, 3:4, 4:3, 9:16, 16:9, 1:2, 2:1), `--seed INT`, `--nsfw-check true`

## API key

- `EVOLINK_API_KEY` env var (required)
- Get key: https://evolink.ai

## Triggers

- Chinese: "生图：xxx" / "出图：xxx" / "生成图片：xxx"
- English: "generate image: xxx" / "generate a picture: xxx"

Treat the text after the colon as `prompt`, use default size `1:1`, generate immediately.

## Notes

- Script prints `MEDIA:<path>` for OC auto-attach — no extra delivery logic needed.
- Fallback (no Python): see `{baseDir}/references/curl_heredoc.md`
- Image saved locally as `.webp`. URL expires ~72h but local file persists.
