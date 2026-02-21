# curl + jq (Unix/macOS, requires jq)

Run in a single shell call (avoid relying on exported variables persisting across tool calls).

Replace:
- `<API_KEY>` with the user's Evolink key
- `<OUTPUT_FILE>` with `evolink-<TIMESTAMP>.png` (or match the actual format returned)
- `<USER_PROMPT>`, `<SIZE>`, `<QUALITY>`, and optionally `<IMAGE_URLS>`

## Text-to-image

```bash
API_KEY="<API_KEY>"
OUT_FILE="<OUTPUT_FILE>"
PROMPT="<USER_PROMPT>"
SIZE="<SIZE>"
QUALITY="<QUALITY>"

JSON_BODY=$(jq -n \
  --arg prompt "$PROMPT" \
  --arg size "$SIZE" \
  --arg quality "$QUALITY" \
  '{model: "gemini-3-pro-image-preview", prompt: $prompt, size: $size, quality: $quality}')

RESP=$(curl -s -X POST "https://api.evolink.ai/v1/images/generations" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d "$JSON_BODY")

TASK_ID=$(echo "$RESP" | jq -r '.id // .task_id // empty')

if [ -z "$TASK_ID" ]; then
  echo "Error: Failed to submit task. Response: $RESP"
  exit 1
fi

MAX_RETRIES=72
for i in $(seq 1 $MAX_RETRIES); do
  sleep 10
  TASK=$(curl -s "https://api.evolink.ai/v1/tasks/$TASK_ID" \
    -H "Authorization: Bearer $API_KEY")
  STATUS=$(echo "$TASK" | jq -r '.status // empty')

  if [ "$STATUS" = "completed" ]; then
    URL=$(echo "$TASK" | jq -r '.results[0] // empty')
    curl -s -o "$OUT_FILE" "$URL"
    echo "MEDIA:$(cd "$(dirname "$OUT_FILE")" && pwd)/$(basename "$OUT_FILE")"
    break
  fi
  if [ "$STATUS" = "failed" ]; then
    echo "Generation failed: $TASK"
    break
  fi
done

if [ "$i" -eq "$MAX_RETRIES" ]; then
  echo "Timed out after max retries."
fi
```

## Image-to-image / editing

Same flow, but add `image_urls` when building the JSON body:

```bash
# After setting PROMPT, SIZE, QUALITY as above:
IMAGE_URLS='["<URL1>", "<URL2>"]'

JSON_BODY=$(jq -n \
  --arg prompt "$PROMPT" \
  --arg size "$SIZE" \
  --arg quality "$QUALITY" \
  --argjson urls "$IMAGE_URLS" \
  '{model: "gemini-3-pro-image-preview", prompt: $prompt, size: $size, quality: $quality, image_urls: $urls}')
```

If you only have a URL and no file yet, download it immediately (URL expires in ~24 hours):

```bash
OUT_FILE="evolink-result.png"
curl -L -o "$OUT_FILE" "<URL>"
echo "MEDIA:$(cd "$(dirname "$OUT_FILE")" && pwd)/$(basename "$OUT_FILE")"
```
