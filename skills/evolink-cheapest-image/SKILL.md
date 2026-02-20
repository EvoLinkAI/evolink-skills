---
name: evolink-z-image
description: Generate images with the Evolink Z-Image-Turbo API (submit, poll, download .webp). Trigger on “生图：<提示词>”, “出图：<提示词>”, “生成图片”, “generate a picture: <prompt>”, or mentions of Evolink / z-image-turbo.
---

# Evolink Z-Image (Z-Image-Turbo)

Use this skill when the user wants to generate an image via Evolink's `z-image-turbo` model.

## Quick flow (生图：提示词)

If the user says `生图：XXXX` (or `出图：XXXX` / `生成图片：XXXX`), treat `XXXX` as the `prompt` and generate immediately.

- Default `size`: `1:1` (only override if the user explicitly asks for a different ratio/size)
- Default `nsfw_check`: `false`
- Do not send progress-style replies like “任务已提交 / 正在生成中…”. Wait and only respond once the image is downloaded and delivered.

## Quick flow (generate a picture: prompt)

If the user says `generate a picture: XXXX` (or similar like `generate an image: XXXX`), treat `XXXX` as the `prompt` and generate immediately (same defaults as above).

## Inputs to collect

- `prompt` (required; ask if missing; max 2000 chars)
- `size` (optional; default `1:1`; allowed: `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `9:16`, `16:9`, `1:2`, `2:1`, or custom `WxH` with 376–1536 px)
- `nsfw_check` (optional; default `false`)
- `seed` (optional; integer)

## API key

Check `EVOLINK_API_KEY` in the environment. If it is empty/unset, ask the user for their key.

- If they don't have one: direct them to register and create a key in the Evolink dashboard.
- After the user provides it: keep it in your conversation context as `EVOLINK_API_KEY` for subsequent generations in this chat.
- For persistence across future chats, suggest setting a system env var:
  - macOS (zsh): `echo 'export EVOLINK_API_KEY="...your key..."' >> ~/.zshrc && source ~/.zshrc`
  - Linux (bash): `echo 'export EVOLINK_API_KEY="...your key..."' >> ~/.bashrc && source ~/.bashrc`
  - Windows (PowerShell): `[Environment]::SetEnvironmentVariable("EVOLINK_API_KEY","...your key...","User")`
- Do not rely on `export`/`set` persisting across separate tool calls; pass the key into the same command you run.

## Preferred execution (Python, no deps)

Prefer running the bundled script (avoids shell escaping issues and brittle JSON parsing).

Run the script from the `scripts/` folder next to this `SKILL.md`:

```bash
python3 scripts/evolink_z_image.py \
  --api-key "$EVOLINK_API_KEY" \
  --prompt "a cat sitting on the moon" \
  --size "1:1" \
  --nsfw-check false
```

If the user provided a `seed`, include `--seed 42`. If they want a specific output filename, include `--out evolink-YYYYmmdd-HHMMSS.webp`.

## Fallback execution (curl + heredoc)

If Python is unavailable, use the curl flow in `references/curl_heredoc.md`.

## Result handling (OpenClaw)

- Always download the image to a local file. Do not only return the URL.
- After download, **do not use the read tool as “delivery”** (read only shows the image in this chat).
- For actual delivery, the agent must call the OpenClaw messaging action and pass the local file via the message `filePath` parameter (pointing at the downloaded `.webp`), then confirm delivery to the user.
- Do not paste the image URL, do not show the local file path, and do not mention URL expiry. Just send the image via OpenClaw and reply succinctly (e.g. “已发送” / “Sent”).
