# NeuroViz plugin for AI assistants

NeuroViz retouches jewelry and product photos, shows your piece worn on a model, and turns a product photo into a short video. This plugin brings it into your AI assistant. Give it a photo, say what you need, and the assistant runs the right NeuroViz tool on your actual photo. When the result is ready, it shows up in the chat with a button to download it at full size.

The plugin has two parts:

- **The NeuroViz connector**, `https://app.neuroviz.ai/mcp`. You sign in once with your NeuroViz account. There are no keys or passwords in this repository.
- **Three skills**: `retouch-jewelry-photo`, `virtual-try-on` and `product-video`. They tell the assistant when NeuroViz fits and how to pick the right tool for your photo, so it reaches for NeuroViz when you ask for a retouch, a try-on or a video, without you naming it.

You need a NeuroViz account, which you can make at [app.neuroviz.ai](https://app.neuroviz.ai). Each job uses credits from that account, and the assistant tells you the cost before anything runs.

## Install

### Claude (web and desktop)

1. Open [Customize > Plugins](https://claude.ai/customize/plugins).
2. Choose **Add**, then **Add marketplace**, and enter `neurovizAI/neuroviz-plugin`.
3. Add **NeuroViz** from the list.
4. Open the plugin's **Connectors** tab, connect NeuroViz and sign in with your NeuroViz account.

The plugin then shows up in Claude Code too, on the same account. On Team and Enterprise plans, an Owner may need to allow the marketplace or add the connector first.

No **Add marketplace** option? Download [`downloads/neuroviz-claude-plugin.zip`](downloads/neuroviz-claude-plugin.zip) and use **Add > Upload plugin** instead.

In Claude, share your photo as a link. A photo attached to your message doesn't reach NeuroViz there yet.

### Claude Code

```
/plugin marketplace add neurovizAI/neuroviz-plugin
/plugin install neuroviz@neuroviz-plugins
```

Then type `/mcp`, pick **neuroviz**, choose **Authenticate** and approve in your browser. From a terminal, the same two steps are `claude plugin marketplace add neurovizAI/neuroviz-plugin` and `claude plugin install neuroviz@neuroviz-plugins`.

### Codex, and Work mode in the ChatGPT desktop app

```bash
codex plugin marketplace add neurovizAI/neuroviz-plugin
codex plugin add neuroviz@neuroviz-plugins
```

You can also install it from the ChatGPT desktop app once the marketplace is added: open the Plugins Directory, choose the NeuroViz marketplace and install NeuroViz. Sign in with your NeuroViz account when asked.

### ChatGPT

We're working on a listing in ChatGPT's plugin directory, which will install the connector and the skills in one step. Until then, if your account or workspace allows custom MCP servers:

1. Go to [chatgpt.com/plugins](https://chatgpt.com/plugins), select **+**, then **Add custom MCP server**.
2. Name it NeuroViz and enter `https://app.neuroviz.ai/mcp`. Set authentication to OAuth, choose **Create as a plugin**, and sign in with your NeuroViz account.
3. At [chatgpt.com/skills](https://chatgpt.com/skills), choose **Add** and upload each skill ZIP from [`downloads/`](downloads/): `retouch-jewelry-photo.zip`, `virtual-try-on.zip` and `product-video.zip`.

In ChatGPT you can attach the photo straight to your message.

### Any other assistant

Connect `https://app.neuroviz.ai/mcp` as a remote MCP server and sign in with OAuth. Our [MCP docs](https://neuroviz.ai/api-docs/mcp/) have the details.

## Try it

- "Retouch this ring photo so it's ready for my Etsy listing."
- "Show this ring on a woman's hand."
- "Make a short video of this bangle turning, for my product page."
- "How many NeuroViz credits do I have left?"

## Privacy

Your photos and results stay private to your NeuroViz account, and we never use them to train AI models. See our [privacy policy](https://neuroviz.ai/privacy-policy/).

## Support

Write to support@neuroviz.ai or visit [neuroviz.ai/help](https://neuroviz.ai/help/).

## What's in this repository

| Path | What it is |
|---|---|
| `plugins/neuroviz/` | The plugin: the three skills, the logos, and the manifest and connector files each assistant reads (`.claude-plugin/plugin.json` and `.mcp.json` for Claude, `plugin.json` and `mcp.json` for ChatGPT and Codex) |
| `.claude-plugin/marketplace.json` | The marketplace Claude reads |
| `.agents/plugins/marketplace.json` | The marketplace Codex and the ChatGPT desktop app read |
| `downloads/` | The plugin as a ZIP for Claude's **Upload plugin**, and each skill as its own ZIP |

We build this repository from NeuroViz's own source, so these are the same skills our server uses, and each build replaces what's here. If something is wrong or missing, open an issue or write to support@neuroviz.ai rather than sending a pull request.

## License

MIT, see [LICENSE](LICENSE). The NeuroViz name and logo are trademarks of NeuroViz Corp. and aren't covered by the license.
