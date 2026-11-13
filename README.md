# Request Concurrency

`@eduwork/dsh-request-concurrency` · v0.1.0-dev · MulanPSL-2.0 AND MIT

Caps how many model request streams may run at once across the whole Host, through a single FIFO queue. Root sessions, ordinary, background and nested subagents, workflow-spawned agents and auxiliary requests such as compaction all wait in the same line, so several workspaces sharing one provider cannot flood it.

A slot belongs to an active model response stream and is released as soon as the stream ends, including while tools run or a task waits for a child. That keeps nested delegation working even with a total of 1.

## Highlights

- Hooks the official `llm/stream` extension point, so every model provider routed through the DSH LLM service is covered by one limit.
- Default total of `3` concurrent requests, with an accepted range of `1` to `64`.
- Limit precedence: `EDUWORK_MAX_CONCURRENT_REQUESTS`, then legacy `EDUWORK_MAX_PARALLEL_SUBAGENTS`, then the plugin's `maxConcurrentRequests`, then legacy `maxParallelSubagents` (an old limit of `N` children becomes `N + 1` requests), then the default.
- Live setting `eduwork-concurrency.maxConcurrentRequests`; lowering the limit lets current requests finish, raising it starts queued ones immediately.
- Saved legacy `maxParallelSubagents` preferences are migrated once to the matching total and removed with the official atomic settings mutation.
- Cancellation removes a queued waiter, and completion, failure or disposal releases the slot.
- A schemastery `Config` in `lib/native.js` exposes the same `maxConcurrentRequests` field for generated plugin configuration.

## Install

Install this directory as a local DSH plugin bundle. From a shell:

```bash
dsh plugin --profile web add /absolute/path/to/request-concurrency
```

Or add the directory through the **Plugins** panel in DSH (local bundle). Restart the DSH app after installation; plugins are activated from the bundle patch (`cordis.patch.yml`).

## Configuration

No configuration keys: the bundle patch inserts the plugin with defaults.

The effective limit is resolved at load time from the environment variables and plugin config above, then exposed to users as `eduwork-concurrency.maxConcurrentRequests` in Settings, persisted in `dsh/settings.yaml`. UI changes apply without a restart; editing the setting by hand needs one.

## Requirements

- DeepSeek Harness >=0.2.1 <0.3.0
- Peer packages: @deepseek-ai/cordis, @deepseek-ai/dsh-llm, @deepseek-ai/dsh-agent, @deepseek-ai/schemastery, @deepseek-ai/dsh-settings, @deepseek-ai/dsh-session

## Notes and limits

- One global limit per Host with no reserved slot for the main conversation, so a busy installation queues its own root session too.
- It does not cover separate clients, external SDK or Codex/Claude processes, scripts calling HTTP directly, image generation or TTS; server-side account limits are still required.
- The official workflow `maxConcurrentAgents` (default `2`) and the official subagent cap (default `2`) are separate settings and are not converted into this limit.
- Development-stage build: the version is `0.1.0-dev`.

## 中文文档

[README.zh-CN.md](./README.zh-CN.md)

## License

MulanPSL-2.0 AND MIT — see [LICENSE](./LICENSE) and, where present, [LICENSE-MIT](./LICENSE-MIT). Extracted from the ECNU EduWork distribution (https://github.com/ECNU/EduWork); upstream copyright and license notices are preserved.
