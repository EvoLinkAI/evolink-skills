# EvoLink Agent Skills

**One API key. All the AI models you need.** Give your AI agent multimodal superpowers.

Works with [OpenClaw](https://github.com/openclaw/openclaw), [Claude  Code](https://claude.ai/code), [Cursor](https://cursor.com), [Gemini CLI](https://github.com/google-gemini/gemini-cli), and any agent supporting the [Agent Skills Spec](https://agentskills.io).

## Install

```bash
clawhub install cheapest-image
clawhub install best-image
```

Or clone and copy:

```bash
git clone https://github.com/EvoLinkAI/evolink-skills.git
cp -r evolink-skills/skills/cheapest-image ~/.openclaw/skills/
cp -r evolink-skills/skills/best-image ~/.openclaw/skills/
```

## API Key

Get yours at [evolink.ai](https://evolink.ai), then:

```bash
export EVOLINK_API_KEY="your-key"
```

## Available Skills

| Skill | What it does | Price |
|-------|-------------|-------|
| [cheapest-image](skills/cheapest-image/) | Possibly the cheapest image generation — text-to-image via EvoLink API | ~$0.0036/image |
| [best-image](skills/best-image/) | Best quality image generation — text-to-image, image-to-image, editing via EvoLink API | ~$0.12-0.20/image |

More skills coming soon — video, music, multi-model routing.

## Docs

- API: [docs.evolink.ai](https://docs.evolink.ai)
- Dashboard: [evolink.ai](https://evolink.ai)

## License

MIT
