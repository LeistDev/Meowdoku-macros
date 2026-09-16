# MeowDoku Auto Solver

**🇺 English** | 🇺 [Русский](README_ru.md)

<img src="MeowLogo.png" alt="MeowDoku" width="140">

An EMScript macro for [Macrorify](https://play.google.com/store/apps/details?id=com.kok_emm.mobile) that solves [MeowDoku](https://play.google.com/store/apps/details?id=com.oakever.meowdoku) levels end to end: it reads the board from the screen, computes a valid cat placement, plays it out with taps, and walks through victory and defeat screens on its own.

## What it does

The macro reads the colored grid directly from the screen, groups cells into color regions, and solves the puzzle with a backtracking search over the game rules: one cat per row, one per column, one per color region, and no two cats touching, including diagonally. The solution is then played out cell by cell using the game's two tap cycle, cross first and cat second. After the last cat the macro waits for the leaderboard, closes it, and starts the next level. The loop runs indefinitely, so the macro can farm levels unattended.

## Requirements

The macro runs on any Android device or emulator that can run Macrorify. Before the first launch, make sure of the following:

- Macrorify is installed and its touch service is configured (Accessibility Service, or Native Service on emulators and Android 7).
- Screen capture permission is granted to Macrorify.
- MeowDoku is installed and logged in, with a level available from the main menu.

## Installation

1. Download the latest `.mrscript` file from the [Releases](../../releases) page.
2. In Macrorify, import the file through the macro import menu.
3. Open MeowDoku and stay on the main menu, or inside any level with a fresh board.
4. Start the macro from the Macrorify overlay.

The macro detects on its own whether it was started from the main menu or from inside a level, and behaves accordingly.

## How it works

Each iteration of the main loop goes through the same stages:

1. Start detection. The screen is probed for a colored grid. If none is found, the macro looks for the orange level button of the main menu and presses it.
2. Board reading. Horizontal and vertical scan lines locate the rows and columns by color saturation, which makes the macro independent of the board size (4x4 to 12x12) and of the device resolution.
3. Region grouping. All cell colors are sampled in a single capture and clustered into color regions with a CIE76 delta E comparison.
4. Solving. A backtracking search places one cat per row under the column, region and non adjacency constraints.
5. Placing. Every target cell receives two taps: the first draws a cross, the second converts it into a cat.
6. End of level. On defeat the retry button is pressed and the level is solved again. On victory the macro waits eight seconds after the last cat, closes the leaderboard, and presses the next level button.

## Configuration

Tunable constants live at the top of the macro file:

| Constant | Default | Meaning |
| --- | --- | --- |
| `PLACE_TAPS` | `2` | Taps per cell, matching the cross then cat cycle |
| `TAP_HOLD` | `50` | Hold time of a single tap, ms |
| `TAP_GAP_INNER` | `150` | Pause between the two taps on the same cell, ms |
| `TAP_GAP` | `300` | Pause between different cells, ms |
| `CLOSE_DELAY` | `8000` | Pause after the last cat before closing the leaderboard, ms |
| `DEBUG` | `true` | Shows debug toasts with the detected board size |

## Device compatibility

Coordinates are stored relative to a 1080x2340 reference screen and rescaled at runtime from `Env.deviceW()` and `Env.deviceH()`, so no manual adjustment is needed on other resolutions. Boards are located by scanning rather than by fixed coordinates, which keeps the macro working across grid sizes and layout shifts.

## Troubleshooting

**The macro does nothing on start.** It expects either the main menu with the orange level button, or a fully rendered board. Close any dialog or splash screen before starting.

**A "Regions X of Y" toast appears.** The color clustering saw extra regions, usually because the board was still animating. The macro retries on its own; if it keeps repeating, raise `COLOR_DE` slightly.

**The leaderboard stays open.** Raise `CLOSE_DELAY` by one or two seconds.

**Cats are not placed.** Check the touch capability in Macrorify; emulators and Android 7 devices need the Native Service.

## Repository layout

- `MeowDoku.mrscript` in Releases: the macro itself
- `MeowLogo.png`: logo used in this README
- `README.md`, `README_ru.md`: documentation in English and Russian

## Disclaimer

This project is independent and is not affiliated with OakEver (MeowDoku) or the Macrorify developers. The macro is provided for personal and educational use; check the terms of service of the game before using automation.
