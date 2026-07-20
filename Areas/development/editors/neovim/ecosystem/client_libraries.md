---
aliases: ["Neovim client libraries", "Neovim RPC clients"]
tags: [neovim, ecosystem, rpc, reference]
---

# Neovim client libraries

The five official language clients that speak msgpack-RPC to a running Neovim instance. Each is both a **library** (script the editor from your language of choice) and a **remote plugin host** (run plugins written in that language inside Nvim).

Companion to [[readme]] (structural overview of the ecosystem) and [[activity_snapshot]] (per-repo tempo). This note goes deeper into what each client is for, how mature it is, and when to pick it.

## The protocol they all speak

Every client is a wrapper around the same wire protocol: **msgpack-RPC over a Unix socket, named pipe, or stdio**. The contract is generated from `neovim/src/nvim/api/` and exposed via `nvim --api-info` as machine-readable metadata. API changes ripple to every client; each library re-generates its bindings from the metadata rather than hand-writing them.

Concretely, all five clients let you do the same things:

- Start a Nvim child process, or attach to a running one (`--listen /tmp/nvim.sock`)
- Call any `nvim_*` API function (`buf_get_lines`, `command`, `feedkeys`, …)
- Subscribe to notifications (autocmd events, `rpcnotify` from Lua)
- Register handlers so Nvim can call back into your process ("remote plugin")

The differences are ergonomics, ecosystem, and heartbeat.

## The five clients at a glance

| Client | Language | Version | Runtime floor | Last commit | 365d commits | Verdict |
|---|---|---|---|---|---:|---|
| pynvim | Python | 0.6.1-dev | Python 3.10+ | 2026-04-10 | 21 | Safe default for Python |
| node-client | TypeScript/JS | 5.4.1-dev | Node ≥14 | 2026-07-14 | 59 | Safe default for JS/TS |
| nvim.net | C# | 0.20.0 | netstandard2.0 | 2026-07-18 | 21 | Recent activity, solo maintainer |
| neovim-ruby | Ruby | 0.10.0 | Ruby ≥2.2 | 2026-06-29 | 3 | Long-tail bugfix only |
| go-client | Go | (module) | Go 1.18 | **2025-03-29** | **0** | ~16 months idle — pin cautiously |

## When to pick which

### Use pynvim if
- Writing Python already, or the plugin ecosystem you need is in PyPI (numpy, pandas, requests).
- You want the most polished remote-plugin experience — pynvim's decorator API (`@plugin`, `@command`, `@autocmd`) is the smoothest of any client.
- Historical note: pynvim was the reference client during Neovim's early years, so many patterns in the docs assume it.

Install: `pip install pynvim` — also needed by `:Python3` for `:!python`-adjacent plugins even if you never write pynvim code yourself.

### Use node-client if
- Writing JS or TS. The TypeScript typings are up-to-date and cover the full API surface.
- Building tooling that already lives in the Node ecosystem (VSCode extension, LSP server, JS-based test runner talking to Nvim).
- Highest current tempo of the five (59 commits in the last year); most likely to pick up new API features quickly.

Install: `npm install neovim`.

### Use go-client with caution
- Fine for **existing** consumers pinned to a known-good commit. `gonvim`, `nvim-go`, and several TUI wrappers depend on it.
- **Not** a good pick for new work today: last commit 2025-03-29, zero commits in the past year. A new Nvim API function added since then won't be exposed. If you want Go + Nvim in 2026, the honest advice is to write the msgpack-RPC calls yourself against `--api-info` output, or wait to see if someone forks and revives it.

### Use neovim-ruby only if
- The tool ecosystem lock-in is real (Rails, existing Ruby CI harness).
- You can accept 3 commits in the last year — it's alive but not developing.

Install: `gem install neovim`.

### Use nvim.net if
- You must speak from .NET (Windows tooling, Visual Studio integration, F# scripting).
- Solo-maintainer risk is acceptable — one person owns it and has kept it moving through 2026.
- The typical use case is powering VSNvim-style embeddings (Nvim inside Visual Studio 2017), but VSNvim itself is dead (last commit 2018).

Install: NuGet `NvimClient.API` (v0.20.0).

## Maturity signals I actually check

Test suite size is a rough proxy for how much of the API each client has integration coverage for:

| Client | Test files | Notes |
|---|---:|---|
| neovim-ruby | 23 | Highest coverage per LOC despite low commit tempo |
| go-client | 18 | Solid coverage, but frozen in time |
| pynvim | 12 | Focused on core RPC + remote-plugin surface |
| node-client | 9 | Coverage badge is on the README, ~90% claimed |
| nvim.net | 1 project | Single `NvimClient.Test` project, contents harder to grep for coverage claim |

## The RPC surface, minimally

Every client exposes a handle usually called `nvim` (or `Neovim`, `Nvim`) with methods matching the C-level API:

```python
# pynvim
from pynvim import attach
nvim = attach('socket', path='/tmp/nvim.sock')
nvim.command('echo "hello"')
lines = nvim.current.buffer[:]
```

```typescript
// node-client
import { attach } from 'neovim';
const nvim = await attach({ socket: '/tmp/nvim.sock' });
await nvim.command('echo "hello"');
const lines = await nvim.buffer.lines;
```

```go
// go-client
v, _ := nvim.Dial("/tmp/nvim.sock")
v.Command(`echo "hello"`)
buf, _ := v.CurrentBuffer()
lines, _ := v.BufferLines(buf, 0, -1, false)
```

```ruby
# neovim-ruby
require 'neovim'
nvim = Neovim.attach_unix('/tmp/nvim.sock')
nvim.command('echo "hello"')
lines = nvim.current.buffer.lines
```

```csharp
// nvim.net
var nvim = new NvimClient(new UnixDomainSocketTransport("/tmp/nvim.sock"));
nvim.Command("echo \"hello\"");
var lines = nvim.GetCurrentBuffer().GetLines(0, -1, false);
```

The mental model is identical everywhere — the differences are Promise vs blocking, method naming (`buffer.lines` vs `BufferLines`), and how the transport is constructed.

## Remote plugin hosts

Beyond scripting, each client can register itself as a **remote plugin host** so plugins written in that language run inside Nvim:

- **pynvim** — decorator-based, most polished. `~/.config/nvim/rplugin/python3/*.py` files auto-load.
- **node-client** — decorator-based (TypeScript-friendly). `rplugin/node/*.js` layout.
- **neovim-ruby** — DSL-based (`Neovim.plugin do |plug| ... end`). `rplugin/ruby/*.rb` layout.
- **go-client** — compiled binaries in `rplugin/go/*`. Higher friction (build step per platform) but faster startup.
- **nvim.net** — remote-plugin host works but rarely used in the wild; native Lua covers most needs.

Since Lua became first-class in Nvim 0.5+, the remote-plugin model has been in gradual decline for anything performance-sensitive or startup-sensitive. Reach for a remote plugin when your logic genuinely lives in a non-Lua ecosystem (e.g. calling a Python ML library from Nvim); otherwise write it in Lua.

## Caveats

- **Client tempo ≠ Nvim compatibility.** A dormant client (go-client) still works for the API surface that existed when it stopped moving. Nvim keeps API changes backward-compatible; new features simply won't appear in the client.
- **"Official" is a status, not a guarantee.** All five live under `github.com/neovim/`, but active development is driven by whoever cares. Neovim core does not staff any client team; each client rises or falls on community energy.
- **The C client mentioned in old docs is gone.** There used to be a `libnvim`/`nvim-client` C library; it was abandoned and dropped from the org. Speak the msgpack-RPC protocol directly from C, or link against pynvim/node-client via FFI.

## Related

- [[readme]] — ecosystem structural map
- [[activity_snapshot]] — commit tempo across all 17 repos
- [[contributors]] — top authors of `neovim/neovim` itself
