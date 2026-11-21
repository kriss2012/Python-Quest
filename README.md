# Python Quest

Python Quest is a browser-based learning game that teaches Python basics through eight interactive code blocks. Players complete variables, functions, conditionals, lists, dictionaries, loops, and arithmetic expressions to build and unlock a small canvas adventure.

The project is intentionally dependency-free. It runs as a single static HTML file, with no build step or package installation required.

## Play

Open `Python Quest.html` in a modern desktop or mobile browser.

For local development, you can serve the project from its root directory:

```bash
python3 -m http.server 8000
```

Then visit `http://localhost:8000/Python%20Quest.html`.

## Code structure

The repository currently contains two source-facing files:

| Path | Purpose |
| --- | --- |
| `Python Quest.html` | Complete application: HTML structure, CSS, game data, JavaScript, and canvas rendering |
| `README.md` | Project overview, usage instructions, and code-structure documentation |

### HTML sections

The page in `Python Quest.html` is organized into these top-level areas:

- **Header and HUD** - displays the hero name, build health, completed blank count, and creator-mode button.
- **How it works** - explains the learning and play loop.
- **Build area** - renders the eight interactive Python code blocks.
- **Save panel** - creates, pastes, and loads progress codes or save files.
- **World editor** - becomes available after victory and lets the player edit monster values.
- **Play area** - contains the canvas map, movement controls, battle UI, hero card, and trophies.
- **Victory dialog** - summarizes wins and gold and offers replay or creator-mode actions.

### JavaScript structure

The application JavaScript is wrapped in an immediately invoked function. Its responsibilities are grouped into named sections:

| Section | Responsibilities |
| --- | --- |
| Blank checkers | Validate text, numbers, operators, and variable names before accepting answers |
| Code blocks | Define lessons, code examples, expected answers, unlock messages, and blank counts |
| State | Initialize, sanitize, load, and save progress in browser storage |
| Code rendering | Convert code block definitions into highlighted HTML and editable inputs |
| Hero, map, and battle | Calculate hero stats, draw sprites and map tiles, handle movement, and run battles |
| Panels | Refresh the HUD, hero card, unlocked sections, world editor, and trophy shelf |
| Events | Connect block buttons, keyboard input, movement controls, and save/load controls |
| Save and load | Export and import validated base64 progress codes |
| Boot | Restore progress and initialize the rendered application |

The main state object `S` tracks:

- Current answers and which blanks have been completed
- Build health
- Victory and creator-mode status
- Monster definitions
- Earned trophies

Runtime play state is kept separately from persistent progress:

- `R` represents the active run: position, level, health, gold, potions, and monsters.
- `B` represents the active battle and its log.

## Learning flow

1. The first code block is open; later blocks unlock only after the previous block is complete.
2. Entering valid answers marks blanks as complete.
3. Invalid answers reduce Build HP; a crash resets Build HP and clears invalid blanks.
4. Completing the first block unlocks hero information, and completing all eight blocks unlocks play.
5. Defeating every monster unlocks victory, replay, and creator mode.

Completing a block can therefore affect:

- The values and state shown in the hero card
- Available battle rules and healing
- Monsters placed on the map
- Whether the play, world editor, and creator features are visible

## Progress storage

Normal progress is stored in `localStorage` under:

```text
pythonQuest.save.v1
```

The Save and Load panel can also export the state as a base64-encoded JSON save code or import a save code from a text/JSON file. Imported data passes through the state sanitizer before it is accepted.

## Creator mode

Creator mode unlocks after all monsters are defeated. It enables:

- Editing completed code blocks again
- Editing monster names and numeric values
- Adding and removing monsters
- Seeing changes in the next run or battle

Monster changes are validated for count, name length, health, attack, and gold limits before they are applied.

## Key data definitions

The central game definitions are declared near the start of the JavaScript:

- `BLOCKS` contains lesson titles, concepts, code lines, blank specifications, and unlock descriptions.
- `TOTAL_BLANKS` is calculated from `BLOCKS` and is used by the HUD.
- `MAP` defines the 12 by 8 grid, including walls and monster starting locations.
- `SPOTS` is derived from reachable map cells.
- `SPR` and `PAL` contain the pixel-art sprite rows and colors.
- `CLASSCOL` maps the three hero classes to display colors.

To add or adjust a lesson, update the relevant entry in `BLOCKS`. Keep the blank indexes in `lines` synchronized with the `blanks` array for that block.

## Development notes

- Keep styles in the existing `<style>` section and keep scripts in the existing IIFE.
- Reuse the `S`, `R`, `B`, `val()`, `hs()`, `refreshAll()`, and `save()` patterns when changing game behavior.
- Use `applyWorld()` for validated monster-editor updates.
- Use `refreshAll()` after state changes that affect multiple UI panels.
- Test with keyboard and on-screen movement, invalid and valid answers, save/load, battle outcomes, victory, and creator mode.