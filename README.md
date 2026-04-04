# timer/

A JSON-driven interval timer built for mobile. No backend, no dependencies — just a single HTML file and JSON routine files hosted on GitHub Pages.

Live at: `https://0xbn.github.io/interval-timer/`

---

## How it works

The app loads routine definitions from JSON files in a `routines/` folder. Each routine is a sequence of typed blocks (prepare / work / rest) that the timer steps through automatically. Text-to-speech announces each block so you can use it screen-free.

---

## File structure

```
interval-timer/
├── index.html              ← the entire app (single file)
└── routines/
    ├── index.json          ← list of routine filenames to load
    ├── abrahangs.json      ← example routine
    └── my-routine.json     ← add more routines here
```

### `routines/index.json`

A JSON array of filenames. The app fetches each file on load and renders a card for it.

```json
["abrahangs.json", "core-warmup.json"]
```

---

## Routine JSON schema

Each routine file is a single JSON object.

```json
{
  "name": "string — displayed as the routine title",
  "description": "string — optional subtitle shown in detail view",
  "sets": 3,
  "blocks": [ ...block objects... ]
}
```

### `sets`

Integer. The entire `blocks` array repeats this many times. The last block in the array can use `"skip_on_last": true` to skip the final rest.

If your routine has varying reps per grip (e.g. 6 sets of one grip, 2 sets of another), expand the blocks explicitly rather than using `sets`.

---

## Block types

Every block is one of three types: `prepare`, `work`, or `rest`.

### `prepare` block

Announces the upcoming exercise. Displayed in orange.

```json
{
  "type": "prepare",
  "label": "4-finger half crimp",
  "duration": 5,
  "countdown_last": 3
}
```

- `label` — spoken aloud as *"Prepare for [label]"*
- `duration` — seconds to display the prepare screen
- `countdown_last` — optional. Beeps on each of the last N seconds

### `work` block

The active exercise. Displayed in green.

```json
{
  "type": "work",
  "label": "4-finger half crimp",
  "duration": 10,
  "countdown_last": 3
}
```

- `label` — spoken aloud as *"Start"* when the block begins (label is shown on screen)
- `duration` — seconds to hold
- `countdown_last` — optional. Beeps on each of the last N seconds

### `rest` block

Recovery period. Displayed in blue. Spoken aloud as *"Rest"*.

```json
{
  "type": "rest",
  "duration": 20,
  "skip_on_last": true
}
```

- `duration` — seconds to rest
- `skip_on_last` — optional boolean. If `true`, this block is skipped on the final set iteration. Use this to avoid a rest at the very end of a routine.

---

## Audio behavior

- **Prepare block starts** → speaks *"Prepare for [label]"*
- **Work block starts** → speaks *"Start"*
- **Rest block starts** → speaks *"Rest"*
- **Last N seconds of any block** → short beep each second (if `countdown_last` is set)
- **Block ends** → single beep
- **Routine finishes** → triple ascending beep + speaks *"Session complete. Well done."*

The voice can be changed via the selector on the home screen. The app auto-selects the best available English voice (prefers Samantha/Karen/neural voices). On iOS, download an Enhanced or Premium voice in Settings → Accessibility → Spoken Content → Voices for best results.

---

## Controls during timer

| Button | Behavior |
|--------|----------|
| **← Back** | If >2s into block: restarts current block. If ≤2s: goes to previous block. At first block: restarts from top. |
| **⏸ Pause / ▶ Resume** | Pauses/resumes the ticker. Speech is not paused. |
| **Skip ›** | Immediately advances to the next block. |
| **✕ Stop** | Stops timer, resets state, returns to home. |

---

## Detail view

Tapping a routine card (not the ▶ play button) opens a detail screen showing every block in order with its type, label, and duration. Useful for reviewing the structure of a routine before running it. The ▶ Start button in the top right launches the timer from there.

Tapping the ▶ play button on the card directly skips the detail view and starts immediately.

---

## Adding a new routine

1. Create a JSON file following the schema above (e.g. `my-routine.json`)
2. Place it in the `routines/` folder
3. Add the filename to `routines/index.json`
4. Push to GitHub — it appears on the home screen on next load

---

## Example: Abrahangs (Emil Abrahamsson no-hang protocol)

10 minutes, twice daily. Feet on the floor, ~40% of max effort.

Sequence: 6× 4-finger half crimp → 6× 3-finger drag → 2× front-2 open → 2× middle-2 open → 2× front-2 half crimp → 2× middle-2 half crimp

Each rep: 10s work / 20s rest. Prepare block fires once per grip group.

See `routines/abrahangs.json` for the full implementation.

---

## Generating routine JSON with an LLM

Describe the routine in natural language and ask for the JSON. Example prompt:

> "Generate a timer routine JSON for the following: 3 sets of 7-second max hangs on a 20mm edge with 3 minutes rest between sets. Add a 5-second prepare block before each hang. Use countdown_last: 3 on the hang blocks. Skip the final rest."

The LLM should output a valid routine object matching the schema above. Paste it into the custom JSON field on the home screen to test, then save it as a file in `routines/` once confirmed.

---

## Paste / test without deploying

Use the **Custom JSON** panel on the home screen. Paste any valid routine JSON and tap **▶ Run** to test it immediately without touching the repo.

---

## Schema summary (for LLM reference)

```typescript
type BlockType = "prepare" | "work" | "rest";

interface Block {
  type: BlockType;
  label?: string;        // displayed and spoken (required for prepare/work)
  duration: number;      // seconds
  countdown_last?: number; // beep on last N seconds
  skip_on_last?: boolean;  // skip this block on final set
}

interface Routine {
  name: string;
  description?: string;
  sets?: number;         // default 1 — repeats the entire blocks array
  blocks: Block[];
}
```
