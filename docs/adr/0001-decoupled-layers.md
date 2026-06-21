# Two decoupled layers bridged only by the tool

Hoskinator stores resumes as a **vanilla rendercv git repo** — it carries no tool-specific artifacts (no bullet IDs, no sidecars), so it can be cloned, branched, diffed, and rendered with `rendercv` by someone who has never heard of Hoskinator. All structured knowledge — the Master Store of entries, bullets, and Variants — lives in a **separate SQLite/Turso database** beside the repo, never inside it.

The two layers share **no stored cross-references**. Data moves one way only: store → tool/human → resume. The single write back into the store is an explicit "save this wording as a Variant." There is no continuous reconciliation of resume text against store bullets, because there is no shared key to reconcile on.

## Consequences

- Reusing a wording across branches is plain manual git (cherry-pick/merge) with no tool support; the tool models no branch hierarchy and offers no promotion feature. This is accepted in exchange for repo purity.
- A bullet written directly in a resume YAML is **not** captured back into the store automatically; pulling it in as a Variant is a manual gesture.
- The store gains queryability (for scoring, MCP, search) at the cost of git history/diffing of its contents.
