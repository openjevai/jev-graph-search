# Setup and credential handling

jev-graph-search accepts a Jev credential from the process environment or a hidden interactive setup prompt. It never accepts an API key as a command-line argument.

For an environment-only run:

```sh
export TYPESAFE_API_KEY='...'
# or export OPENROUTER_API_KEY='...'
# or export OPENJEV_API_KEY='...'
jev-graph-search search "query" --input ./snapshot.json
```

To persist the exported key for later runs:

```sh
jev-graph-search setup --from-env
```

To choose a provider explicitly:

```sh
jev-graph-search setup --provider typesafe --from-env
jev-graph-search setup --provider openrouter --from-env
jev-graph-search setup --provider openjev --from-env
```

In a TTY, `jev-graph-search setup` shows a provider menu. Use ↑/↓ to select TypeSafe, OpenRouter, or OpenJEV and Enter to confirm, then paste your API key into the hidden prompt. TypeSafe is selected initially. Press Esc or Ctrl+C to cancel the menu. `--provider` skips the menu. In a noninteractive process setup exits with an actionable message and recommends `--from-env`; it never waits for hidden input.

The credential file remains `${XDG_CONFIG_HOME:-$HOME/.config}/jevgraph/credentials.env`, with a `0700` parent and `0600` file. The legacy `jevgraph` directory and `JEVGRAPH_*` environment names are deliberate compatibility paths, so existing users do not need to run setup again after the rename. Environment values take precedence over saved values. `TYPESAFE_API_KEY` is preferred over the compatibility `JEV_API_KEY` alias. `JEVGRAPH_PROVIDER=typesafe|openrouter|openjev` selects a provider explicitly; if multiple providers remain applicable without a selection, resolution fails rather than guessing.

For the built-in CLI endpoints, `TYPESAFE_API_KEY` is sent only to `https://api.typesafe.ai`, `OPENROUTER_API_KEY` only to `https://openrouter.ai`, and `OPENJEV_API_KEY` only to `https://api.openjev.sh`. A provider selection never forwards the other provider's key. Programmatic clients that supply a custom `baseUrl` are responsible for choosing a trusted endpoint and keeping its key scoped to that configured provider. Keep keys, tokens, and other secret credential content out of queries, memory, prompts, snapshots, logs, caches, and artifacts.

`jev-graph-search config` prints only configured/provider/model/source metadata. `jev-graph-search doctor` adds boolean environment-presence checks and states that no live authentication was attempted. Neither command prints key material.

Semantic retrieval uses the bounded persistent cache supplied by the package when enabled. Its default location remains `${XDG_CACHE_HOME:-$HOME/.cache}/jevgraph` for compatibility; `--no-cache` passes `enabled: false`, bypassing disk and memory reuse.

OpenRouter uses the native Decisions transport and the verified model `typesafe/jev-1.13`; direct Jev uses `jev-latest` by default; OpenJEV uses the OpenJEV gateway endpoint `https://api.openjev.sh/v1/systemone` with model `openjev`. The package does not invent chat-completion fallbacks or silently switch providers.

Semantic commands may override the selection with `--provider typesafe|openrouter|openjev` and `--model MODEL`. A forced provider must have its matching key; it does not fall back to another configured key.

Semantic `search` sends the user query and `place` sends its text or memory. `audit --semantic` sends bounded passages from selected pages as query and candidate data. Each request includes only selected bounded graph page titles, aliases, and content excerpts and goes to the selected provider (`https://api.typesafe.ai`, `https://openrouter.ai`, or `https://api.openjev.sh`). Explain this transfer before first semantic use unless it is already clear from the request or setup. Local input does not imply a local model: `--offline` keeps ranking local. Reading a local graph does not authorize sending the entire workspace to a semantic provider; limit egress to evidence required for the current task.
