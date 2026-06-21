# The engine mediates git via libgit2

The Web frontend is a v1 target and has no shell, so every git operation a tailoring session needs — `branch`, `checkout`, `commit`, `status`, `diff`, `log`, `cherry-pick` — is a **JSON-RPC method the engine implements**. The engine is a full git client, not just a store+LLM box.

It performs these operations through **libgit2 (the `git2` crate)** rather than shelling out to the system `git`, so frontends receive **typed results** (structured diffs, logs, status) instead of porcelain text that an interactive UI would have to parse.

## Consequences

- libgit2 is **not byte-identical to the `git` CLI**: user **hooks do not fire**, and some config nuances differ. It writes standard git objects, so the repo stays vanilla (ADR-0001) and the user's own `git` keeps working alongside the tool.
- Shell-out was rejected: parsing `git` porcelain into JSON for a web UI is fragile, despite its perfect parity with the user's manual git.
