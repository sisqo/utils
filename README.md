# utils

Every developer ends up with a personal drawer of small tools — the ones you
reach for without thinking, that never make it into a client's codebase but
save you five minutes a dozen times a day. This is mine: a growing collection
of scripts I actually use, kept in one place so a new machine is one
`git clone` away from feeling like home.

No frameworks, no dependencies to speak of — just plain, self-contained
scripts built to be dropped on the `PATH` and forgotten about until they're
needed again.

## Layout

Each tool lives under `bin/` as a single self-contained executable script, meant
to be symlinked (or copied) into a directory on your `PATH`, e.g.:

```sh
ln -s "$(pwd)/bin/<script>" ~/.local/bin/<script>
```

## Scripts

### `bin/cla`

Interactive picker that lists the projects under `~/git` and launches `claude`
inside the one you choose.

- Arrow keys (or a quick number/letter shortcut) to select a project, `q` to quit.
- Assigns each project a deterministic color (derived from its name) and starts
  `claude` with that color and the project name as label.
- `r` and `c` toggle Remote Control and the Claude in Chrome integration
  (`--chrome`), each shown as a status line under the list. Both start on; the
  state at the moment you confirm is the one `claude` launches with.
- Falls back to a plain `select` menu when stdin isn't a terminal (no toggles
  there — both stay on).

Usage:

```sh
cla
```

### `bin/cla2`

`cla` in cockpit mode: same job, same options, dressed up as a full-screen
TUI. The original `cla` is untouched; both give a project the same default
color (a color picked in `cla2` applies to `cla2` only).

- **Boot log** in `dmesg` style while it scans `~/git`, with real timestamps
  and each repo's branch and dirty state (skip it with `CLA2_NOBOOT=1`).
- A **gradient banner that scrolls** (`a` pauses it; `CLA2_STATIC=1` starts it
  paused) next to a neofetch-style system block: host, OS, kernel, claude
  version, uptime/load, a RAM meter and the `/color` palette.
- **Project list** ordered as: the current directory on top, then the pinned
  projects (by shortcut digit), then everything else alphabetically, with
  `── preferiti ──` / `── altri ──` separator rows in between (skipped by
  keys and mouse). While searching the list is flat. Git/dirty status and a
  scrollbar, plus an **inspector**
  for the selected project: branch and ahead/behind vs. upstream, working tree
  state, HEAD, remote, commit count, a 16-week commit sparkline, a language
  bar, whether `CLAUDE.md` and `.claude/` exist, and a short commit log.
- **Keys:** `↑/↓` or `j/k` to move, `g/G` to jump to the top or bottom,
  `1`–`9` to launch the project pinned to that digit, `/` for fuzzy search
  (matched letters get highlighted), `r`/`c` to toggle Remote Control and
  Chrome, `q`/`Esc` to quit.
- **Shortcuts are yours to assign:** `p` then `1`–`9` pins the selected
  project to that digit (a project already holding it loses its pin), `p`
  then `0` (or `p` again) unpins it, any other key cancels.
- **Colors:** `o` cycles the selected project's `/color` through the palette
  and back to `auto` (the default, derived from the name).
- Pins and colors only work for projects directly under `~/git` and are saved
  in `~/.config/cla2/projects` (`$XDG_CONFIG_HOME` is honored), one
  `name<TAB>digit<TAB>color` line per project; either field may be empty. The
  file is rewritten on every change, so hand-written comments don't survive.
  Invalid lines are ignored; entries for folders that no longer exist are kept
  and keep their digit reserved until you reassign it.
  **Mouse:** click to select, click again to launch, scroll wheel to move.
- Without a terminal on stdin it falls back to a plain `select` menu in the
  same order.
- A powerline status bar and a **launch sequence**: session rename, then
  `git pull` with a spinner, then the `exec` line before `claude` starts.
- Needs a truecolor terminal and a Nerd Font for the icons.
