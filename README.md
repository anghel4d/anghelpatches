# Anghelpatches

Fixes, patches, and field notes from [@anghel4d](https://github.com/anghel4d). Small, self-contained writeups of real problems I hit and actually fixed — one file per problem, each written to stand alone: symptom, environment, root cause, copy-pasteable fix, verification. Sibling to the feature forks that carry the same name (`Anghelpatch <feature> for <project>`): [wezterm](https://github.com/anghel4d/wezterm) (scanline-sweep font rasterizer), [helium](https://github.com/anghel4d/helium) (tabgroup clipboard).

## House style (so entries stay useful and reproducible)

- Symptom-forward title. Lead with the words a person would actually type or paste, not the internal cause. "Text renders white inside Neovim's terminal" beats "base16 slot ordering".
- Self-contained. No "see my dotfiles." Everything needed to understand and apply the fix is in the file.
- State the environment explicitly — tools and versions — so a reader can tell if it's their case.
- Separate root cause from fix. Explain *why*, then give the change. Name the common misdiagnoses and say why they're wrong; that's what makes it a complete answer.
- Copy-pasteable fix, minimal first, then the robust/general version.
- Verification step. Show how to confirm it worked.
- Terse, flat prose. No decorative bold. Real code, not pseudocode.

## Index

- [Text renders white/invisible inside Neovim's `:terminal` on a light base16 theme](base16-nvim-light-theme-terminal-colors-inverted.md) — base16-nvim derives `terminal_color_*` from base16 slots, which inverts ANSI 0/7/15 on light themes; breaks any hosted TUI (e.g. Claude Code) that paints text with ANSI black.
