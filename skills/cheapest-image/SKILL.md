---
name: cheapest-image
description: Possibly the cheapest AI image generation (~$0.0036/image). Text-to-image via the EvoLink API.
homepage: https://evolink.ai
metadata: {"openclaw": {"emoji": "🖼️", "requires": {"env": ["EVOLINK_API_KEY"]}, "primaryEnv": "EVOLINK_API_KEY"}}
---

# EvoLink Cheapest Image

Generate images via the EvoLink z-image-turbo API.

## API Endpoint

- Base: `https://api.evolink.ai/v1`
- Submit: `POST /images/generations`
- Poll: `GET /tasks/{id}`

## Step 1 — Submit Task

```json
{
  "model": "z-image-turbo",
  "prompt": "<USER_PROMPT>",
  "size": "<SIZE>",
  "nsfw_check": <true|false>
}
```

Optional field: `"seed": <INT>`

| Parameter | Values |
|---|---|
| size | 1:1, 2:3, 3:2, 3:4, 4:3, 9:16, 16:9, 1:2, 2:1 |
| nsfw_check | `true` / `false` (default `false`) |
| seed | any integer (optional, for reproducibility) |

## Step 2 — Poll for Result

`GET /tasks/{id}` — poll every 10 s, up to 72 retries (~12 min).

Wait until `status` is `completed` or `failed`.

## Step 3 — Download & Output

Download the URL from `results[0]`. Auto-detect format from URL (webp/png/jpg). Save as `evolink-<TIMESTAMP>.<ext>`.

Print `MEDIA:<absolute_path>` for OC auto-attach.

## Reference Implementations

| Platform | File |
|---|---|
| Python (all platforms, zero deps) | `{baseDir}/references/python.md` |
| PowerShell 5.1+ (Windows) | `{baseDir}/references/powershell.md` |
| curl + bash (Unix/macOS) | `{baseDir}/references/curl_heredoc.md` |

## API Key

- `EVOLINK_API_KEY` env var (required)
- Get key: https://evolink.ai

## Triggers

- Chinese: "生图：xxx" / "出图：xxx" / "生成图片：xxx"
- English: "generate image: xxx" / "generate a picture: xxx"

Treat the text after the colon as `prompt`, use default size `1:1`, generate immediately.

## Notes

- Script prints `MEDIA:<path>` for OC auto-attach — no extra delivery logic needed.
- Image saved locally (format auto-detected from URL). URL expires ~24h but local file persists.
