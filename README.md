# interval-timer

URL-driven **interval timer** for mobile — turns compact routine strings into prepare / work / rest blocks with spoken cues.

No backend, no npm runtime deps for the timer itself, no routine-file workflow required for normal use.

**Live:** https://0xbn.github.io/interval-timer/

Related:

- [`workout`](https://github.com/0xBN/workout) — training program + coaching (source of truth for what to train)  
- [`life-ops`](https://github.com/0xBN/life-ops) — ops playbook (points here)

## How it works

Pass a compact routine in the `x` query param, or paste the same text on the home screen.

Blocks:

- `prepare`
- `work`
- `rest`

Designed **audio-first**: follow without watching the screen; put setup/transition speech in the compact string. Use unlabeled `rest` only for quiet recovery.

## Main format

Use `@N` inside tokens when a block needs a countdown.

```text
https://0xbn.github.io/interval-timer/?x=Abrahangs~4-finger%20half%20crimp~p5@3,w10@3,r20,w10@3,r20,w10@3,r20,w10@3,r20,w10@3,r20,w10@3,r20
```

General shape:

```text
?x=Routine Name~Label Section 1~Pattern 1~Label Section 2~Pattern 2
```

## Compact routine grammar

```text
Routine Name~Label~pattern~Label^Prepare Label~pattern
```

Rules:

- `~` separates routine sections
- a label section is followed by one pattern section
- `Label` — same text for prepare and work
- `Work Label^Prepare Label` — prepare speech differs from the work label

Pattern tokens:

- `p5` / `p5@3` — prepare (optional countdown last N)
- `w10` / `w10@10` — work
- `r20` — rest
- `r6^Switch to left side` — rest with spoken cue
- `r10!` — rest with `skip_on_last`

Write repeated blocks explicitly, e.g. `p5@3,w10@3,r20,w10@3,r20,w10@3`.

Audio-first example:

```text
Single leg bridge^Right side ready~p5@3,w30@10,r6^Switch to left side,w30@10,r10^Rest!
```

## Speech behavior

- `prepare` speaks the prepare label
- `work` speaks `Start`
- `rest` speaks its label if present, otherwise `Rest`
- `countdown_last` via `@N` on the token

If a transition matters hands-free, encode it as a spoken prepare label or spoken rest cue.

## Home screen

- Built-in routines from `routines-catalog.json`
- Pasted compact routine text
- Direct launch from URL

## Catalog

`routines-catalog.json` is the consolidated routine source in-repo: built-in library, bridge from older JSON files, and a reference for compact URLs.

## LLM usage

Use `llm-compact-routine-instructions.txt` when asking an LLM to generate routines (format, Abrahangs example, prompt template).

## Abrahangs example

See `abrahangs-url-example.txt` for before/after URL comparison and a compact Abrahangs URL.

## Legacy

Older `?r=` JSON URLs still work; preferred format is `?x=...`.

## Local dev

```bash
npx --yes serve .
```

Deploy: GitHub Pages (`https://0xbn.github.io/interval-timer/`).
