# rendercv is the canonical resume representation

The only supported resume format is **rendercv YAML**, and it is the canonical, primary artifact — the per-branch file living in the git repo. The Master Store mirrors rendercv's entry-type taxonomy (experience, education, normal, publication, one-line, bullet, text) so store content maps cleanly into the YAML.

Non-rendercv formats (Typst, Markdown, plain text) are **out of scope for v1**. If added later they will be downstream transpiles of the rendercv YAML — never independently tailored or rendered from the Master Store. This keeps each tailored resume single-sourced in its YAML.

## Considered options

- A coarse two-shape store model (detailed vs list entries) and treating rendercv as one export among several. Rejected: with rendercv as the sole target, mirroring its exact type taxonomy avoids lossy mapping at codegen time. Format-independence is deferred rather than designed-in.
