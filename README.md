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

### `bin/vmclean`

Frees disk space on this VM by clearing caches and leftovers, always showing a
preview first. Nothing is deleted until you pick what to remove and confirm.

- Styled like `cla2`: a gradient banner with host and disk info, a
  `dmesg`-style scan log, framed panels with size bars, a powerline summary,
  and `[ OK ]` steps with a spinner while deleting. `VMCLEAN_NOANIM=1` skips
  the animations, `NO_COLOR=1` (or piping the output) drops the colors.
- The preview lists every category with its size and what's inside (paths,
  snap revisions, log files, orphan packages). It needs no `sudo`.
- **Recommended** categories are safely regenerable: npm/npx and pip caches,
  thumbnails, old Claude Code versions and installer downloads, the Claude
  Desktop and VS Code caches, leftover Chrome headless profiles, disabled snap
  revisions, the apt cache, orphan apt packages, leftovers of removed packages
  (`dpkg --purge` of `rc` packages, `/lib/modules` dirs of uninstalled
  kernels), the systemd journal (vacuumed to 200M), rotated logs in
  `/var/log`, and `/var/crash`.
- **Optional** ones have a cost, which the preview spells out: the active
  JetBrains IDE's cache (forces a full reindex), Playwright/Puppeteer browsers,
  Chrome's and Chromium's disk caches, `.next` build dirs under `~/git`, the
  unpacked Hyprland source trees in `~/src/hypr/w` (`build.sh` re-fetches
  them; `.deb`s and tarballs stay), the `tmp` dirs of finished, unpinned
  Claude jobs, uninstalling the Chromium snap altogether (`snap remove
  --purge`, profile included), old JetBrains versions' dirs, the Trash, and
  the clipboard history (`cliphist wipe`). Picking the Chromium uninstall
  drops its cache category, and totals don't count the overlap twice.
- It never touches sessions and config in `~/.claude`, the active Claude Code
  version (nor one a running session is still executing), project
  `node_modules`, or installed kernels (old ones are filtered out of the
  orphan packages).
- It skips a category when the program using it is running (Chrome,
  Chromium, Claude Desktop, VS Code, the JetBrains IDE, a Next.js server, a
  test browser, an MCP server started through `npx`). Its own parent
  processes don't count, so launching it from a shell whose command line
  mentions those names doesn't trigger a skip.
- At the prompt: `c` for the recommended categories, `t` for all of them,
  numbers like `1 3 5` to pick, Enter for nothing. A recap panel and a final
  `[s/N]` confirm it. `sudo` is only requested then, and only if a chosen
  category needs it. Each step then reports the space it actually freed.

Usage:

```sh
vmclean            # preview, then choose what to delete
vmclean --preview  # preview only
```
