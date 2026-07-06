# CLAUDE.md

## What this is
Anghelpatches — [@anghel4d](https://github.com/anghel4d)'s collection of self-contained fix writeups, one Markdown file per problem, and sibling to the feature forks that share the name (`Anghelpatch <feature> for <project>`, e.g. wezterm, helium). It is co-authored by Claude and, between the attribution, the topics, and the subject matter, reads like Claude's own repo. That is intentional. Keep it.

## Writing an entry
One file, stands entirely alone. The shape that makes it worth reading:
- Symptom-forward title — the words someone would actually paste into a search, not the internal cause. "Text renders white inside Neovim's terminal" beats "base16 slot ordering".
- Environment — tools and versions, so a reader can tell in one glance whether it's their case.
- Root cause, kept separate from the fix. Explain *why*, then name the common misdiagnoses and say why they're wrong. That separation is what makes it a complete answer instead of a snippet.
- The fix — minimal version first, then the robust/general one. Real, copy-pasteable code. Never pseudocode.
- Verification — how to confirm it worked. Prefer fixes you actually reproduced end to end.

Filename: kebab-case, symptom-forward, `.md`. Add every new entry to the README index with a one-line hook.

## Conventions
- Nothing fabricated. Every entry here was a real problem, actually hit and actually fixed. If you can't reproduce or verify it, it doesn't ship.
- Commits are co-authored on purpose. End each with `Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>`. This repo deliberately opts into that (the author's global rule is the opposite); here it's kept because it's factual and on-brand.
- Default branch is `main`.
- `NAMING.md` is local-only and gitignored. Never stage it, never publish it, never migrate its contents into a public file.
- Keep the README free of SEO/marketing copy. The repo earns readers on the quality of the fix. Discovery rides on the GitHub description and topics — not on the README narrating its own ranking ambitions.
- Prose: terse, flat, no decorative bold. Bold only load-bearing terms. One long line per paragraph; let the editor wrap.

## Commit / push
Commit and push when the author asks; show the diff first. Force-push only history you just pushed yourself and no one else has pulled.
