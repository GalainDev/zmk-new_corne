# Eyelash Corne Layout Plan

This document is the working source of truth for the next keymap iteration for the Eyelash Corne.

## Status

Implemented in [eyelash_corne.keymap](/Users/heman/Downloads/keyboard/zmk-new_corne/config/eyelash_corne.keymap):

- Base
- Nav
- Num
- Code
- Util
- base-layer home-row mods
- thumb layer-taps
- `nice-view-gem` display integration via `build.yaml`, `west.yml`, and `eyelash_corne.conf`

Not yet completed:

- compile verification
- on-device typing validation
- on-device symbol-layer validation
- timing tuning based on real use

## Goals

- Keep the seller's `eyelash_corne` board support and hardware behavior intact.
- Keep a QWERTY base layer.
- Borrow the useful structural ideas from Miryoku without copying it wholesale.
- Add home-row mods carefully for coding use.
- Build a coding-focused symbol layer instead of using the current mixed-purpose symbol layer.
- Keep the rotary encoder and right 5-way switch useful.
- Make implementation incremental so problems are easy to isolate.

## Constraints

- This board is not a standard Corne. Firmware changes must stay in the seller config repo.
- The left half has a rotary encoder.
- The right half has a 5-way switch.
- The right 5-way already acts as arrows on base and as mouse movement/click on non-base layers.
- Home-row mods can introduce timing issues if applied too aggressively.

## Current Seller Layout Summary

Base layer:

- QWERTY alphas
- left encoder press: `Space`
- right 5-way: arrows plus center press `Enter`
- left thumb outer: `LGUI`
- left thumb middle: `mo 1`
- left thumb inner: `lt 3 SPACE`
- right thumb inner: `lt 3 ENTER`
- right thumb middle: `mo 2`
- right thumb outer: `RALT`

Other current behaviors:

- encoder rotation: volume on base
- encoder rotation: scroll on non-base layers
- 5-way: mouse movement and click on Number, Symbol, and Fn layers

## Design Decisions

### Base Layer

- Keep QWERTY.
- Add home-row mods on the base layer only.
- Keep thumb `GUI`.
- Avoid overloading the base with too many dual-role keys.

### Base Layer Home-Row Mod Assignment

Recommended v1 assignment:

- left hand: `A=Ctrl`, `S=Alt`, `D=GUI`, `F=Shift`
- right hand: `J=Shift`, `K=GUI`, `L=Alt`, `;=Ctrl`

Notes:

- This is the macOS-oriented `CAGS` mirrored assignment for QWERTY.
- `GUI` remains on the left thumb even though it is also available on the home row.
- The thumb `GUI` should remain the primary choice for macOS shortcuts that feel awkward with home-row `GUI`.
- If `GUI` proves too error-prone on the home row, the first rollback should be:
  - `A` back to plain `A`
  - `;` back to plain `;`
  - keep `S/D/F` and `J/K/L` as `Alt/GUI/Shift`

Why this is the recommended starting point:

- it follows the macOS modifier frequency guidance from the home-row-mods guide
- it preserves symmetry across both hands
- it keeps `Command` on a stronger finger than the pinky
- it is easy to partially roll back later without redesigning the whole base layer

### Home-Row Mods

Base layer only:

- Use home-row mods on both hands.
- Keep `GUI` available on the thumb even if it is also tested on a home-row position later.
- Do not assume home-row mods should exist on every layer.

Implementation defaults to test first:

- use ZMK hold-tap `balanced` behavior
- set `require-prior-idle-ms = 150`
- set `tapping-term-ms = 280`
- set `quick-tap-ms = 175`
- prefer positional hold-tap behavior so opposite-hand presses are more likely to trigger holds
- tune timings after real typing tests instead of guessing them perfectly up front

Why not use Miryoku timings directly:

- In the local Miryoku ZMK repo, the shared `u_mt` behavior is `tap-preferred` with `U_TAPPING_TERM = 200`.
- That is a broad default, but it is not a dedicated Eyelash-Corne coding HRM profile.
- For this board and use case, we want a more defensive starting point that is closer to the ZMK hold-tap guidance for home-row mods.

Non-base layers:

- Use plain modifiers where needed on home-row positions.
- Do not use hold-tap home-row mods on the Code layer.

Reasoning:

- This follows the general advice from the precondition guide more closely than putting HRMs everywhere.
- It also matches Miryoku more closely: Miryoku uses HRMs on the base layer, but plain modifiers on Num and Sym layers.

## Planned Layers

The planned working set is five layers:

1. Base
2. Nav
3. Num
4. Code
5. Util

### Base

Purpose:

- normal typing
- home-row mods
- access to primary layers from thumbs

### Nav

Purpose:

- arrows
- home/end/page movement
- word movement
- editing/navigation helpers

Navigation cluster:

- use `J K L ;` for arrows
- likely mapping: `J=Left`, `K=Down`, `L=Up`, `;=Right`

Home-row modifiers on Nav:

- yes, but use plain modifiers, not home-row mod-taps
- keep modifiers on the left home row so the right hand can handle navigation
- starting point: `A=Ctrl`, `S=Alt`, `D=GUI`, `F=Shift`

Nav layer v1 draft:

Left side:

- top row: `Esc`, `Tab`, `Ins`, `Home`, `End`, `Del`
- home row: `GUI`, `Alt`, `Ctrl`, `Shift`, `Caps Word`, `Enter`
- bottom row: `RAlt`, `Bspc`, `Del`, `PgDn`, `PgUp`, `Trans`

Center hardware on Nav:

- 5-way up: mouse up
- 5-way left: mouse left
- 5-way press: left click
- 5-way right: mouse right
- 5-way down: mouse down
- encoder rotation: scroll

Right side:

- top row: `Trans`, `Home`, `PgDn`, `PgUp`, `End`, `Del`
- home row: `Trans`, `Left`, `Down`, `Up`, `Right`, `Enter`
- bottom row: `Trans`, `Bspc`, `Del`, `Trans`, `Trans`, `Esc`

Notes:

- This keeps the layer OS-agnostic for the first implementation.
- `Trans` slots are intentional placeholders for later editor-specific or macOS-specific commands.
- If we later add macOS/editor navigation helpers, they should replace `Trans` positions first.

### Num

Purpose:

- Miryoku-inspired number layout
- plain modifiers on home-row positions if needed
- easier access to number entry and modifier-plus-number shortcuts
- preserve a clean path into navigation for future Vim-style count workflows

### Code

Purpose:

- custom symbol layer for coding
- optimize for rolls, pairs, and common coding bigrams
- keep it separate from Bluetooth, RGB, and bootloader controls

Code layer style:

- use a rolls-first layout
- prioritize common coding pairs over standard keyboard mnemonic placement
- still keep rough groups so the layer remains learnable

### Util

Purpose:

- Bluetooth controls
- RGB controls
- output switching
- bootloader and reset
- low-frequency maintenance actions

Util layer v1 draft:

Left side:

- top row: `BT_CLR_ALL`, `BT_SEL 0`, `BT_SEL 1`, `BT_SEL 2`, `BT_SEL 3`, `BT_CLR`
- home row: `RGB_OFF`, `RGB_ON`, `RGB_EFF`, `RGB_EFR`, `RGB_BRI`, `RGB_BRD`
- bottom row: `OUT_USB`, `OUT_BLE`, `Studio Unlock`, `Bootloader`, `Sys Reset`, `Mute`

Center hardware on Util:

- 5-way: mouse movement and left click
- encoder rotation: RGB brightness
- encoder press: play/pause

Right side:

- top row: `F1`, `F2`, `F3`, `F4`, `F5`, `F6`
- home row: `F7`, `F8`, `F9`, `F10`, `F11`, `F12`
- bottom row: `macOS Screenshot`, `Scroll Lock`, `Soft Off`, `Ins`, `Del`, `Esc`

## Thumb Cluster Plan

Agreed starting point:

Left thumb cluster:

- outer: `GUI`
- middle: tap `Tab`, hold `Nav`
- inner: tap `Space`, hold `Num`

Right thumb cluster:

- inner: tap `Enter`, hold `Code`
- middle: `Backspace`
- outer: tap `RAlt`, hold `Util`

Notes:

- This keeps the highest-value actions on thumbs: `Space`, `Enter`, `Backspace`, layer access, and `GUI`.
- `RAlt` stays on the right outer thumb for the first pass because removing thumb `Alt` too early may be premature.
- If later testing shows `RAlt` is low value, the right outer thumb is the first candidate for `Delete` or another function.

## Hardware Plan

### Rotary Encoder

Keep:

- base layer rotation: volume

Use on other layers:

- Nav/Code/Util rotation: likely scroll

### Right 5-Way Switch

Keep:

- base layer: arrows plus center press `Enter`

Use on non-base layers:

- mouse movement and click

This is already supported in the current keymap and should be preserved.

## Number Layer Direction

Planned direction:

- use a Miryoku-style number layout as the starting point
- use plain modifiers, not home-row mod-taps, on this layer

Reason:

- preserves access to shortcuts such as `Cmd+Shift+4`
- avoids unnecessary hold-tap ambiguity while a layer is active
- keeps the door open for Vim-style count then motion sequences such as `5j`, `12k`, or similar editor workflows

Additional design rule:

- the Num layer should support easy transition into Nav
- this does not require simultaneous Num+Nav chording
- the main target is fast sequential use:
  - enter Num
  - type count
  - move into Nav
  - execute motion

Implication for Num design:

- keep the right hand focused on count entry
- keep the left hand available for modifiers and layer transition keys
- reserve at least one obvious position on Num for switching to Nav later if testing shows it is needed

Num layer v1 draft:

Left side:

- top row: `Tab`, `LBKT`, `N7`, `N8`, `N9`, `RBKT`
- home row: `Ctrl`, `Alt`, `GUI`, `Shift`, `EQUAL`, `Trans`
- bottom row: `RAlt`, `GRAVE`, `N1`, `N2`, `N3`, `BSLH`

Center hardware on Num:

- 5-way up: mouse up
- 5-way left: mouse left
- 5-way press: left click
- 5-way right: mouse right
- 5-way down: mouse down
- encoder rotation: scroll

Right side:

- top row: `Trans`, `Trans`, `Trans`, `Trans`, `Trans`, `Bspc`
- home row: `Trans`, `N4`, `N5`, `N6`, `SEMI`, `Enter`
- bottom row: `Trans`, `MINUS`, `N0`, `DOT`, `Nav`, `Esc`

Thumb behavior on Num:

- left outer: `GUI`
- left middle: `Nav`
- left inner: `Trans`
- right inner: `Code`
- right middle: `Bspc`
- right outer: `RAlt` or `Util` depending on implementation details

Notes:

- This is inspired by Miryoku's flipped/right-hand number arrangement, adapted to this board's thumb plan.
- `Nav` on the right-bottom region is intentional as a future-friendly path for Vim count then motion workflows.
- Left home-row modifiers are plain modifier keys, not hold-taps.
- `Bspc`, `Enter`, and `Esc` stay easy to reach while the layer is active.

## Code Layer Direction

The Code layer should be custom, not the stock Miryoku symbol layer.

Design principles:

- put common paired delimiters near each other
- keep `()`, `{}`, and `[]` easy to roll
- make `->`, `!=`, and `::` easy
- keep slash and backslash easy to find
- keep underscore and minus convenient

Initial planning inputs:

- use the Reddit comment as a design reference, not as a layout to copy blindly
- optimize for coding, not for matching a standard number row

Candidate symbols to prioritize:

- `(` `)` `{` `}` `[` `]`
- `<` `>`
- `-` `_`
- `=` `+`
- `/` `\\` `|`
- `:` `;`
- `!` `@` `#` `$` `%` `^` `&` `*`
- `` ` `` `~`

Code layer v2 draft:

Left side:

- top row: `Tab`, `` ` ``, `~`, `#`, `&`, `|`
- home row: `GUI`, `!`, `:`, `=`, `_`, `$`
- bottom row: `RAlt`, `%`, `?`, `*`, `+`, `BSLH`

Center hardware on Code:

- 5-way up: mouse up
- 5-way left: mouse left
- 5-way press: left click
- 5-way right: mouse right
- 5-way down: mouse down
- encoder rotation: scroll

Right side:

- top row: `CARET`, `LPAR`, `RPAR`, `LBKT`, `RBKT`, `Delete`
- home row: `AT`, `LBRC`, `RBRC`, `EQUAL`, `GT`, `Enter`
- bottom row: `FSLH`, `UNDER`, `LT`, `GT`, `DQT`, `Esc`

Thumb behavior on Code:

- left outer: `GUI`
- left middle: `Nav`
- left inner: `Num`
- right inner: `Trans`
- right middle: `Bspc`
- right outer: `RAlt` or `Util` depending on implementation details

Roll/pair intent:

- `{}` live on the right home row for easy rolling
- `()` are directly above `{}`
- `[]` are adjacent to `()` on the same top row
- `=` and `>` sit on the right home row because they often follow closing delimiters
- `<` stays directly below `>` for comparison/operator memory
- `-` can move off the right home row because `=` and `>` have higher priority there
- `!`, `:`, and `=` are grouped on the left home row to support `!=`, `:=`, and `::`
- `_` remains on the left home row because it is a common singleton in code
- slash and backslash remain easy to find on the bottom edges

Known tradeoffs:

- This is optimized for programming flow, not for matching a standard symbol row.
- Some symbols may move after real use, especially `@`, `$`, `%`, and `?`.
- We should test whether `=` on both hands is useful redundancy or whether the left-hand `=` should become `-`.

## Implementation Phases

### Phase 1: Base and Thumbs

- keep existing seller keymap as fallback
- update the thumb cluster
- add base-layer home-row mods
- keep the existing non-base layers temporarily where possible
- confirm typing stability before redesigning everything else

Success criteria:

- typing is stable
- no unacceptable `Space` or `Enter` lag
- home-row mods do not misfire excessively

### Phase 2: Nav Layer

- implement `J K L ;` arrows
- add nearby nav/editing keys
- preserve mouse use of the 5-way where appropriate

Success criteria:

- layer can replace normal navigation comfortably
- one-handed thumb-hold plus opposite-hand navigation feels natural

### Phase 3: Num Layer

- replace seller number layer with a Miryoku-inspired number layout
- place plain modifiers on home-row positions if needed
- confirm number entry and modifier-plus-number shortcuts

Success criteria:

- number entry is comfortable
- `Cmd+Shift+4` and similar shortcuts remain easy

### Phase 4: Code Layer

- build a custom symbol layer around coding frequency and rolling comfort
- keep utility actions out of this layer

Success criteria:

- common coding punctuation is faster than on the current layout
- layer feels coherent rather than memorized by brute force

### Phase 5: Util Layer

- move Bluetooth, RGB, bootloader, reset, and output controls here
- keep rarely used maintenance actions isolated

Success criteria:

- maintenance keys are available but do not pollute daily-use layers

## Open Questions

- exact home-row modifier assignment on base:
  - strict Miryoku-style
  - conservative coding-first variant
- whether thumb `RAlt` survives after testing
- final arrangement of Code layer symbols
- whether any Nav keys should be editor-specific
- whether to keep a dedicated plain `Delete` on the main grid or move it to thumbs/layers

## Working Principle

Do not implement the final layout in one jump.

Preferred order:

1. stabilize base typing
2. stabilize thumbs
3. build Nav
4. build Num
5. build Code
6. isolate Util

This document should be updated whenever a decision changes during implementation.
