# OpenJEV Support

This fork adds optional [OpenJEV](https://openjev.sh) support alongside the existing TypeSafe and OpenRouter providers. TypeSafe remains the default; anyone with a TypeSafe key sees zero behaviour change.

## What was added

- `src/jev.js` — `openjev` provider: endpoint `https://api.openjev.sh/v1/systemone`, model `openjev`, key from `OPENJEV_API_KEY`.
- `src/config.js` — `openjev` added to the `PROVIDERS` set, `keyForProvider`, credential detection/resolution, serialization, and the `configured` check.
- `src/cli.js` — `openjev` added to `--provider` validation, setup `--from-env` key lookup, interactive provider menu, config/doctor model and `environment_keys` output.
- `README.md` — OpenJEV note after the project intro; Configuration section updated.
- `docs/setup.md` — OpenJEV documented alongside TypeSafe and OpenRouter.
- `skills/jev-graph-search/SKILL.md` — OpenJEV endpoint listed in the egress section.
- `skills/jev-graph-search/references/schema.md` — OpenJEV listed in the provider boundaries section.

## Provider selection rule

1. Explicit choice wins: `JEVGRAPH_PROVIDER=openjev` (env or saved credentials) or `--provider openjev`.
2. Otherwise, if `TYPESAFE_API_KEY` (or `JEV_API_KEY`) is set → TypeSafe (unchanged default).
3. Otherwise, if `OPENROUTER_API_KEY` is set → OpenRouter.
4. Otherwise, if only `OPENJEV_API_KEY` is set → OpenJEV.
5. If multiple provider keys are present without an explicit selection, resolution fails rather than guessing.

## How to configure

```sh
export OPENJEV_API_KEY='...'
jev-graph-search setup --provider openjev --from-env
# or:
jev-graph-search search "query" --input ./vault --provider openjev
```

## How it was verified

A live POST request to `https://api.openjev.sh/v1/systemone` with model `openjev`, state `ping`, and one `noul` question returned HTTP 200. A grep for `api.typesafe.ai` confirmed no hardcoded TypeSafe default was replaced or removed — TypeSafe remains the default endpoint when no OpenJEV provider is selected.

## Upstream

Original project: https://github.com/Emlembow/jev-graph-search by @Emlembow.
