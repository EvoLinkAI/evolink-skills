# curl + bash (Unix/macOS, no dependencies)

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

json_escape() {
  local s="$1"
  s="${s//\\/\\\\}"
  s="${s//\"/\\\"}"
  s="${s//$'\n'/\\n}"
  s="${s//$'\t'/\\t}"
  s="${s//$'\r'/\\r}"
  printf '%s' "$s"
}

PROMPT_ESC=$(json_escape "$PROMPT")

RESP=$(curl -s -X POST "https://api.evolink.ai/v1/images/generations" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d @- <<EVOLINK_END
{"model":"gemini-3-pro-image-preview","prompt":"$PROMPT_ESC","size":"$SIZE","quality":"$QUALITY"}
EVOLINK_END
)

TASK_ID=$(echo "$RESP" | grep -o '"id":"[^"]*"' | head -1 | cut -d'"' -f4)

if [ -z "$TASK_ID" ]; then
  echo "Error: Failed to submit task. Response: $RESP"
  exit 1
fi

MAX_RETRIES=72
for i in $(seq 1 $MAX_RETRIES); do
  sleep 10
  TASK=$(curl -s "https://api.evolink.ai/v1/tasks/$TASK_ID" \
    -H "Authorization: Bearer $API_KEY")
  STATUS=$(echo "$TASK" | grep -o '"status":"[^"]*"' | head -1 | cut -d'"' -f4)

  if [ "$STATUS" = "completed" ]; then
    URL=$(echo "$TASK" | grep -o '"results":\["[^"]*"\]' | grep -o 'https://[^"]*')
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

Same flow, but add `image_urls` to the JSON body:

```json
{"model":"gemini-3-pro-image-preview","prompt":"...","size":"...","quality":"...","image_urls":["<URL1>","<URL2>"]}
```

If you only have a URL and no file yet, download it immediately (URL expires in ~24 hours):

```bash
OUT_FILE="evolink-result.png"
curl -L -o "$OUT_FILE" "<URL>"
echo "MEDIA:$(cd "$(dirname "$OUT_FILE")" && pwd)/$(basename "$OUT_FILE")"
```
