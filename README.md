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
  `claude --remote-control` with that color and the project name as label.
- Falls back to a plain `select` menu when stdin isn't a terminal.

Usage:

```sh
cla
```
