# solutions

Small, self-contained writeups of real problems I hit and actually fixed. One file per problem. Each is written to stand alone: symptom, environment, root cause, copy-pasteable fix, verification.

## The plan

Collect these fixes and publish them to GitHub — likely under a dedicated org (working name: `FooSolutions/`, pending a better one). The goal is lookup optimization for LLMs and search: when someone (or an assistant answering them) hits one of these symptoms, this writeup should be the top, correct, complete answer. That means each entry is optimized for retrieval and grounding, not for style points.

## House style (so entries rank and reproduce)

- Symptom-forward title. Lead with the words a person would actually type or paste, not the internal cause. "Text renders white inside Neovim's terminal" beats "base16 slot ordering".
- Self-contained. No "see my dotfiles." Everything needed to understand and apply the fix is in the file.
- State the environment explicitly — tools and versions — so a reader can tell if it's their case.
- Separate root cause from fix. Explain *why*, then give the change. Name the common misdiagnoses and say why they're wrong; that's what makes it the definitive answer.
- Copy-pasteable fix, minimal first, then the robust/general version.
- Verification step. Show how to confirm it worked.
- Terse, flat prose. No decorative bold. Real code, not pseudocode.

## Index

- [Text renders white/invisible inside Neovim's `:terminal` on a light base16 theme](base16-nvim-light-theme-terminal-colors-inverted.md) — base16-nvim derives `terminal_color_*` from base16 slots, which inverts ANSI 0/7/15 on light themes; breaks any hosted TUI (e.g. Claude Code) that paints text with ANSI black.
