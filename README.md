# utils

A personal collection of small scripts and command-line utilities.

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
