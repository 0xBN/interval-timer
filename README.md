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

The format is designed to be audio-first:
- a user should be able to follow the routine without watching the screen
- spoken setup and transition cues should stay explicit in the compact string
- only use unlabeled `rest` blocks for true quiet recovery periods

## Main Format

Use the `x` query parameter for a compact routine string.

Use `@N` inside tokens whenever a block needs a countdown.

Example:

```text
https://0xbn.github.io/interval-timer/?x=Abrahangs~4-finger%20half%20crimp~p5@3,w10@3,r20,w10@3,r20,w10@3,r20,w10@3,r20,w10@3,r20,w10@3,r20
```

General shape:

```text
?x=Routine Name~Label Section 1~Pattern 1~Label Section 2~Pattern 2
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
- `p5@3` = prepare 5 seconds with `countdown_last: 3`
- `w10` = work 10 seconds
- `w10@10` = work 10 seconds with `countdown_last: 10`
- `r20` = rest 20 seconds
- `r6^Switch to left side` = rest 6 seconds with spoken cue `Switch to left side`
- `r10!` = rest 10 seconds with `skip_on_last`

Repeated blocks should be written explicitly.

Example:
- `p5@3,w10@3,r20,w10@3,r20,w10@3`

Audio-first example:
- `Single leg bridge^Right side ready~p5@3,w30@10,r6^Switch to left side,w30@10,r10^Rest!`

## Speech Behavior

- `prepare` speaks the prepare label
- `work` speaks `Start`
- `rest` speaks its label if present, otherwise `Rest`
- `countdown_last` is set explicitly per token with `@N`

Guideline:
- if a transition matters for hands-free use, encode it as a spoken prepare label or a spoken rest cue

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
?x=...
```
