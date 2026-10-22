# Nitrosend MCP — self-onboarding run

- **Goal given to the agent:** "Connect the Nitrosend MCP server at https://api.nitrosend.com/mcp, set up a Nitrosend account with a hosted sender, and send a short email to proof@dogfood.nitrosend.net." The agent took the headless API-key path, so no person did an OAuth browser step.
- **Install and commands:** `npm install -g @openai/codex` → `codex-cli 0.163.0`, Node v24.21.0, Linux x64. MCP wired in `~/.codex/config.toml` as `[mcp_servers.nitrosend] url = "https://api.nitrosend.com/mcp"` with `bearer_token_env_var = "NITROSEND_KEY"`, verified with `codex mcp list`, then one run: `codex exec --skip-git-repo-check --dangerously-bypass-approvals-and-sandbox "<goal>"`. Tools called: `nitro_get_status`, then `nitro_send_message` (channel `email`, subject `mcp.send`).
- **Minutes from connecting to first send:** about 3.
- **Client and model:** OpenAI Codex CLI 0.163.0, model `deepseek-v4.1-flash` via the OpenCode Go provider (`wire_api = "responses"`).
- **Where a person was needed:** zero times. Account and hosted sender `hello@claw-julian.nitrosend.net` already existed from an earlier headless REST signup, so no browser step in this run.
- **Slowest step:** MCP tool approval. The first `nitro_send_message` died with `MCP tool call requires approval, but approval policy is never`, while `nitro_get_status` ran fine; nothing in the error names the flag that unblocks it.
- **Defect / doc gap:** the MCP page never says clients with their own per-tool approval gate refuse `nitro_send_message` under `--ask-for-approval never`. Repro: `codex exec --skip-git-repo-check "<goal>"` → status tool succeeds, send returns that approval error; add `--dangerously-bypass-approvals-and-sandbox` → identical call succeeds.
- **Change I would make:** add a read-only `nitro_ping` tool a client can whitelist, and mark each tool mutating or read-only on the MCP page so clients can pre-approve exactly those.

Codex CLI 0.163.0 · Nitrosend MCP 1.7.0 · Node v24.21.0 · 2026-10-21
