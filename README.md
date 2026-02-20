# EvoLink Agent Skills

**One API key. All the AI models you need.** Give your AI agent multimodal superpowers.

Works with [OpenClaw](https://github.com/openclaw/openclaw), [Claude  Code](https://claude.ai/code), [Cursor](https://cursor.com), [Gemini CLI](https://github.com/google-gemini/gemini-cli), and any agent supporting the [Agent Skills Spec](https://agentskills.io).

## Install

```bash
clawhub install evolink-cheapest-image
```

Or clone and copy:

```bash
git clone https://github.com/EvoLinkAI/evolink-skills.git
cp -r evolink-skills/skills/evolink-cheapest-image ~/.openclaw/skills/
```

## API Key

Get yours at [evolink.ai](https://evolink.ai), then:

```bash
export EVOLINK_API_KEY="your-key"
```

## Available Skills

| Skill | What it does |
|-------|-------------|
| [evolink-cheapest-image](skills/evolink-cheapest-image/) | Possibly the cheapest image generation — text-to-image, image editing via EvoLink API |

More skills coming soon — video, music, multi-model routing.

## Docs

- API: [docs.evolink.ai](https://docs.evolink.ai)
- Dashboard: [evolink.ai](https://evolink.ai)

## License

MIT
