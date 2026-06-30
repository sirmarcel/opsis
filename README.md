# opsis

Nice and simple command-line output for Python. Fka [`comms`](https://github.com/sirmarcel/comms)

> **Note:** This package is experimental — the API may change between releases.

## What is this?

`opsis` gives you a clean, consistent way to print structured output from CLI applications. It handles the fiddly bits — column alignment, truncation, spinners, timing — so you can focus on what your tool actually does.

It's particularly useful for scripts and tools that perform a sequence of discrete tasks (parsing files, calling APIs, running checks, etc.) where you want to show progress without cluttering the terminal.

## Install

```bash
pip install opsis
```

## Quick start

```python
from opsis import Comms

comms = Comms(prefix="myapp")

comms.announce("Starting up")
comms.talk("Processing input files...")
comms.warn("Something looks off!")
```

This produces neatly aligned, prefixed output like:

```
[myapp]         Starting up
[myapp]         -> Processing input files...
[myapp]         ** Something looks off!
```

## Features

### Messages

```python
comms.talk("Regular message")           # prefixed, indented with ->
comms.announce("Important heading")     # bold, followed by a blank line
comms.warn("Something went wrong")      # bold red, indented with **
```

### Multi-line messages

Print structured, multi-line output with an optional boldened title:

```python
comms.state("Line one\nLine two\nLine three", title="Results")
comms.state(["Also works", "with lists"])
```

### Reporter

For multi-step operations, use a `Reporter`. It manages tasks, steps, timing, and animated spinners for you:

```python
reporter = comms.reporter()

reporter.start("building project")       # begins a timed task
reporter.step("compiling sources")       # shows a spinner while waiting
# ... do work ...
reporter.step("linking binaries")        # finishes previous step, starts next
# ... do work ...
reporter.done()                          # prints completion with elapsed time
```

Each step is automatically timed. While a step is running, a spinner animates in-place. When the next step starts (or `done()` is called), the previous step's duration is printed:

```
[myapp]         start building project ...
[myapp]         .. compiling sources                              ✓ (1.203s)
[myapp]         .. linking binaries                               ✓ (0.042s)
[myapp]         done building project!                            ✓ (1.245s)
```

For fast, repetitive operations, use a ticker instead of a spinner:

```python
reporter.step("processing files", spin=False)
for i, path in enumerate(files):
    process(path)
    reporter.tick(f"file {i}")           # updates in-place, throttled
```

You can still use `comms.talk()` and `comms.warn()` during a reporter session — they print cleanly without disrupting the spinner.

### Custom prefix

Every `Comms` instance takes a `prefix` that appears in brackets on each line. Use this to identify your tool:

```python
comms = Comms(prefix="deploy")
# [deploy]        -> uploading artifacts...
```

### Silent mode

Pass `silent=True` to suppress all output from a `Comms` instance — handy for a `--quiet` flag. Every method becomes a no-op, and any `reporter()` it creates inherits the silence, so spinners and tickers stay quiet too:

```python
comms = Comms(prefix="myapp", silent=True)
comms.talk("you will never see this")
reporter = comms.reporter()              # also silent
```

Pass `silent` explicitly to `reporter()` to override per reporter.

### TTY detection

Spinners and tickers are automatically disabled when stdout is not a TTY (e.g. when piping to a file or running in CI). Your output stays clean and parseable without any extra configuration.

## Dependencies

Just [click](https://click.palletsprojects.com/) (for styled terminal output).

## License

MIT
