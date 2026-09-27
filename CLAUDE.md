# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A personal collection of self-contained scripts in `bin/`, each symlinked into
`~/.local/bin` (e.g. `~/.local/bin/cla -> ~/git/utils/bin/cla`). There is no
build step, no dependencies to install, no test suite and no linter
(`shellcheck` is not installed). `README.md` documents every script in English:
when you add a script or change its behavior, update its section there.

Current scripts:
- `bin/cla`: interactive picker for `~/git` projects that `exec`s `claude` in the
  chosen one.
- `bin/cla2`: the same launcher as a full-screen truecolor TUI. `cla` stays
  independent of it: when asked to change `cla2`, don't touch `cla`.

## Conventions

- Comments and user-facing strings inside the scripts are in Italian. README and
  commit messages are in English (commit subjects are prefixed with the script
  name, e.g. `cla: ...`).
- `claude` is invoked by absolute path (`/home/user/.local/bin/claude`) because
  the scripts are launched from Hyprland `exec` binds, which don't have
  `~/.local/bin` in `PATH`.
- `project_color` and its `PALETTE` order must stay identical in `cla` and
  `cla2`, so a project gets the same `/color` whichever launcher starts it.
- Both scripts pass the session name via `-n` (and `--remote-control <name>`),
  because the single initial prompt is already taken by `/color <color>`.

## Checking changes

- Syntax: `bash -n bin/<script>`.
- TUI behavior: run it in a detached tmux session, then drive it with
  `tmux send-keys` and read it with `tmux capture-pane -p`. Resize with
  `tmux resize-window` to exercise layouts. Mouse clicks can be sent as raw SGR
  sequences with `tmux send-keys -l $'\033[<0;X;YM'`. `CLA2_NOBOOT=1` skips the
  boot animation.
- Selecting a project ends in `exec claude`, so to test the launch path, run a
  copy of the script whose `CLAUDE_BIN` points to a fake script that prints its
  arguments (and answers `--version`).
- Nerd Font glyphs don't show up in `capture-pane` output. The real terminal is
  foot with JetBrainsMono Nerd Font Mono, where they are one cell wide.

## cla2 internals worth knowing

- No `set -e` (only `-uo pipefail`): arithmetic that evaluates to 0 must not
  kill the TUI mid-draw.
- Nerd Font glyphs are defined once as `$'\uXXXX'` variables (`G_*`). Keep
  them that way: private-use characters typed literally get stripped when the
  file is written.
- Rendering: each frame is built into the string `F` and printed in one go.
  Panel borders are placed by absolute cursor positioning. Any text that goes
  into a panel is truncated (`trunc`) *before* colors are added, so its visible
  width is always known. A line that overflows the last column wraps and
  corrupts the layout.
- `draw_full` redraws everything after a key press or resize; `draw_tick` only
  redraws the header (animated gradient, system info) and the status bar. The
  main loop's `read -t` timeout (0.1s while animating, 1s otherwise) is the tick.
- Git data: `scan_project` collects the cheap per-repo state at startup.
  `inspect` computes the heavy inspector data for the selected project only,
  and caches it in `CACHE` keyed by `index:width`.
- The Hyprland binds (`ALT`+`plus` → `cla`, `ALT`+`è`/`egrave` → `cla2`, both
  through `foot`) live in `~/.config/hypr/hyprland.conf`. Changes to binds or
  launch setup are also documented in `~/git/hyprland-ubuntu-parallels`
  (`docs/shortcuts.md`).
