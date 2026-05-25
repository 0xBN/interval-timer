# timer/

A URL-driven interval timer built for mobile. No backend, no dependencies, and no routine-file workflow required.

Live at: `https://0xbn.github.io/interval-timer/`

## How It Works

The app runs a routine from a compact URL parameter or from compact text pasted into the home screen.

Internally, the timer still uses blocks of:
- `prepare`
- `work`
- `rest`

But you no longer need to author raw JSON routine files for normal use.

## Main Format

Use the `x` query parameter for a compact routine string and `cd` for the work countdown.

Example:

```text
https://0xbn.github.io/interval-timer/?x=Abrahangs~4-finger%20half%20crimp~p5,w10,r20,w10,r20,w10,r20,w10,r20,w10,r20,w10,r20&cd=3
```

General shape:

```text
?x=Routine Name~Label Section 1~Pattern 1~Label Section 2~Pattern 2&cd=3
```

## Compact Routine Grammar

Each routine is:

```text
Routine Name~Label~pattern~Label^Prepare Label~pattern
```

Rules:
- `~` separates routine sections
- a label section is followed by one pattern section
- `Label` means the same text is used for prepare and work
- `Work Label^Prepare Label` lets prepare speech differ from the work label

Pattern tokens:
- `p5` = prepare 5 seconds
- `w10` = work 10 seconds
- `r20` = rest 20 seconds
- `r10!` = rest 10 seconds with `skip_on_last`

Repeat shorthand:
- `p5x2` = two prepare blocks of 5 seconds
- `w10x6` = six work blocks of 10 seconds
- `r20x5` = five rest blocks of 20 seconds

Example:
- `p5,w10,r20x5,w10`

## Speech Behavior

- `prepare` speaks the prepare label
- `work` speaks `Start`
- `rest` speaks its label if present, otherwise `Rest`
- `countdown_last` is controlled by `cd` in the URL for compact routines

## Home Screen

The home screen now supports:
- built-in routines loaded from [routines-catalog.json](/abs/path/C:/Users/Brian%20Nguyen/code/test/interval-timer/routines-catalog.json)
- pasted compact routine text
- direct launch from URL

## Catalog

[routines-catalog.json](/abs/path/C:/Users/Brian%20Nguyen/code/test/interval-timer/routines-catalog.json) is the consolidated routine source kept in the repo.

It exists mainly as:
- a built-in routine library
- a migration bridge from the older JSON-file setup
- a reference source for converting routines to compact URLs

## LLM Usage

Use [llm-compact-routine-instructions.txt](/abs/path/C:/Users/Brian%20Nguyen/code/test/interval-timer/llm-compact-routine-instructions.txt) when asking an LLM to generate new routines.

That file includes:
- the exact compact format
- an Abrahangs example
- a reusable prompt template

## Abrahangs Example

See [abrahangs-url-example.txt](/abs/path/C:/Users/Brian%20Nguyen/code/test/interval-timer/abrahangs-url-example.txt) for:
- before/after URL comparison
- exact character counts
- a compact URL version of Abrahangs

## Legacy Note

The app still accepts the older `?r=` JSON URL format for compatibility, but the intended format going forward is:

```text
?x=...&cd=...
```
