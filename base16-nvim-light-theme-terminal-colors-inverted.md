# Text renders white/invisible inside Neovim's `:terminal` on a light base16 theme

## Symptom

You run a TUI program inside Neovim's built-in terminal — Claude Code, a REPL, `htop`, anything that colors its own text — and the text comes out white, washed-out, or invisible on a light background. The exact same program run directly in your terminal emulator (Alacritty, WezTerm, kitty, iTerm2, …) looks perfect. It only breaks inside `:terminal`, and only on a light colorscheme. Switch to a dark scheme and it's fine.

It reads like the foreground and background got swapped: whatever should be near-black ink renders near-white.

## Environment

- Neovim with the `RRethy/base16-nvim` colorscheme (or classic `chriskempson/base16-vim` — same mapping).
- A light base16 theme (`vim.o.background = "light"`, `base00` is a light "paper" color).
- Any child program in `:terminal` that paints text with ANSI color 0 (`SGR 30` / `ESC[30m`) expecting it to be dark. Claude Code's light "ansi" themes do exactly this.

## Root cause

base16-nvim derives Neovim's 16 terminal colors from base16 *slots*, not from a real ANSI palette. From `lua/base16-colorscheme.lua`:

```lua
vim.g.terminal_color_0  = M.colors.base00   -- ANSI "black"
vim.g.terminal_color_7  = M.colors.base05   -- ANSI "white"
vim.g.terminal_color_8  = M.colors.base03   -- ANSI "bright black"
vim.g.terminal_color_15 = M.colors.base07   -- ANSI "bright white"
-- ...
```

That mapping bakes in an assumption from the base16 spec: `base00` is the **darkest** slot (the background) and `base07` is the **lightest**. That holds for dark themes, so ANSI 0 lands on near-black and everything works.

On a light theme the ordering flips. `base00` is now the *lightest* color (the paper) and `base05`/`base07` are the dark ink. So:

- ANSI 0 ("black") = `base00` = light paper → **near-white**
- ANSI 7 ("white") = `base05` = ink → near-black
- ANSI 15 ("bright white") = `base07` = darkest → inverted too

A child program that sets its text to ANSI black gets your paper color instead. White text on a light ground → invisible. That's the bug.

Two things that are commonly blamed but aren't the cause:

- **Not the outer terminal.** Once you're inside `:terminal`, Neovim owns the palette via `g:terminal_color_*`; the host emulator's ANSI colors don't apply. So the outer terminal being WezTerm vs Alacritty vs kitty makes no difference — it reproduces identically in all of them.
- **Not the child program's theme.** The program is correctly emitting ANSI 0 for dark text. Neovim is handing it the wrong color for slot 0.

## The fix

After `setup()`, on light backgrounds, replace the inverted ANSI ends with sane values. base16-nvim assigns `terminal_color_*` inside `setup()`, so your override must run **after** it.

Minimal, works for any single base16 light theme:

```lua
require("base16-colorscheme").setup(colors)

if vim.o.background == "light" then
  local c = require("base16-colorscheme").colors  -- resolved base16 palette
  vim.g.terminal_color_0  = c.base05   -- ANSI black       -> ink
  vim.g.terminal_color_7  = c.base01   -- ANSI white       -> light gray
  vim.g.terminal_color_8  = c.base04   -- ANSI bright black -> muted
  vim.g.terminal_color_15 = c.base07   -- ANSI bright white -> lightest
end
```

That un-inverts the two ends (0/7 and 8/15) that base16's slot ordering gets backwards on light themes. The colored slots (1–6, 9–14) are already fine because they map to the `base08..base0F` accents, which don't depend on light-vs-dark ordering.

### Robust version: set a real ANSI palette

If you maintain one palette across several surfaces (terminal emulator + Neovim + a TUI theme), don't derive the terminal colors from base16 slots at all — define an actual 16-color ANSI palette once and set all sixteen `terminal_color_*` explicitly, so every surface renders identically. `terminal_color_0..15` is `[ansi black,red,green,yellow,blue,magenta,cyan,white]` then `[bright black,red,green,yellow,blue,magenta,cyan,white]`:

```lua
-- ansi = 16 hex strings in terminal_color_0..15 order (ansi 0-7 then bright 0-7)
local function set_terminal_ansi(ansi)
  for i = 0, 15 do
    if ansi[i + 1] then vim.g["terminal_color_" .. i] = ansi[i + 1] end
  end
end

require("base16-colorscheme").setup(colors)
set_terminal_ansi(my_light_ansi_palette)   -- must run AFTER setup()
```

This also fixes a subtler divergence in the other direction: on dark base16 themes, base16-nvim feeds the *bright* accents (e.g. `base08` bright red) into the *normal* ANSI slots, so `:terminal` colors don't match what your terminal emulator shows for the same theme. Setting the real palette makes them agree.

## Verify

Boot Neovim and read back the slot that was wrong:

```
nvim --headless -u ~/.config/nvim/init.lua \
  -c 'lua io.stderr:write(vim.o.background.." ANSI0="..tostring(vim.g.terminal_color_0).."\n")' \
  -c 'qa!'
```

On a light theme, `ANSI0` should now be your ink color, not the paper color.

Caveat: Neovim reads `terminal_color_*` when a `:terminal` is **created**, not on every redraw. Changing the palette won't recolor an already-open terminal — close and reopen it (or restart Neovim). Applying the theme at startup covers any terminal you open afterward.

## Why this matters for Claude Code specifically

Claude Code's light themes based on `light-ansi` render body text as `ansi:black` so they track the terminal's own palette. Direct in a terminal emulator with a correct palette, that's ink. Hosted inside a Neovim `:terminal` on a light base16 theme, `ansi:black` resolves to `terminal_color_0 = base00 = paper`, and the whole conversation renders in near-white. The fix above is entirely on the Neovim side; no change to the Claude Code theme is needed.
