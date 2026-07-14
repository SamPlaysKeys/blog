# Adventures in OpenCode2: What I Learned Trying to Switch

I've been a happy OpenCode (V1) user for a while, relying on Vertex AI for my daily coding sessions and the DCP plugin to keep my context from ballooning out of control. When OpenCode2 dropped, I was curious — faster, sleeker, better agent support? Sign me up.

Spoiler: it wasn't that simple.

## The Install

First surprise: V2 ships as a separate binary — `opencode2`. You install it with `npm install -g @opencode-ai/cli@next`. Both V1 and V2 live side by side. That's actually nice; you can test drive without nuking your setup.

## My Config Didn't "Just Work"

The docs say V2 reads your V1 config and translates it in memory. That's mostly true — but "mostly" is doing a lot of work here. My `opencode.jsonc` had V1-era field names like `"provider"` (singular) and `"plugin"` (singular). V2 expects `"providers"` and `"plugins"` (plural). The in-memory translation caught some of this, but not everything.

The real headache came with Vertex AI. My V1 config had:

```json
"google-vertex": {
  "options": {
    "location": "global",
    "project": "my-project"
  }
}
```

V2 has a separate `google-vertex-anthropic` integration (for serving Claude models through Vertex). The problem? V2 discovered it automatically from the Models.dev catalog, but it **didn't inherit** the `project` setting from the `google-vertex` config. Different provider ID, different settings. So any attempt to use a Claude model through Vertex would fail with a cryptic "Not Found" error — no project configured.

## The Plugin Problem

This was the dealbreaker. I use `@tarquinen/opencode-dcp` (Dynamic Context Pruning) to keep token usage sane. V2's migration guide has a blunt warning: **V1 plugins will not work in V2.** The plugin API is completely different, and it's still being finalized. The DCP plugin author would need to port it to the new API, and that hasn't happened yet.

Without DCP, my sessions would balloon in context. Not worth the upgrade for me right now.

## Config Format Whiplash

The config migration guide between V1 and V2 is thorough but dense. Here's just a sampling of what changed:

| V1 | V2 |
|---|---|
| `"provider"` | `"providers"` |
| `"plugin"` | `"plugins"` |
| `"options"` | `"settings"` |
| `"npm"` | `"package"` (with `aisdk:` prefix) |
| `"permission"` (object) | `"permissions"` (array of rules) |
| `"agent"` | `"agents"` |
| `"command"` | `"commands"` |

The trick is that V2 accepts both formats and auto-translates, but the translation has edge cases — especially with provider-specific settings and built-in integrations.

## What I Liked

- **Separate binary** — low-risk testing
- **Better model picker** — `/models` is genuinely nicer
- **Provider introspection** — `opencode2 api get /api/provider/<id>` gives you real-time insight into what's configured
- **The service model** — `opencode2 service status` and `opencode2 service restart` make debugging cleaner than V1

## Bottom Line

OpenCode2 is promising, but it's beta software. If you rely on V1 plugins or have complex multi-provider setups, hold off until the plugin API stabilizes and your favorite plugins get ported. For simpler setups with built-in providers (OpenAI, Anthropic directly), it's worth a test drive.

Me? I'm back on V1 for now. But I'll be keeping an eye on V2.
