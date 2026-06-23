# Chess Master

A beginner-friendly chess game that runs entirely in the browser — no install, no server, no dependencies. Open `index.html` and play.

## Features

- **Full rules enforcement** — all legal moves only: castling, en passant, pawn promotion, check, checkmate, and stalemate
- **Move highlighting** — click a piece to see valid destinations (green dots for moves, red borders for captures)
- **Check indicator** — the king's square turns red when in check
- **Algebraic notation** — move history recorded in standard SAN (e.g. `e4`, `Nf3`, `O-O`, `Qxf7#`)
- **Captured pieces** — displayed in the sidebar sorted by value
- **Undo** — step back through the full game history one move at a time
- **Piece guide** — always-visible reference card explaining how each piece moves
- **New Game** — reset to the starting position at any time
- **3D tabletop board** — the board tilts via CSS perspective, with gradient squares, a framed edge, and a pulsing glow on the king when in check
- **Sound effects & background music** — synthesized move/capture sounds and an ambient music loop, each independently toggleable (no audio files — generated live via the Web Audio API)
- **Dark/light mode** — toggle the page theme (background, panels, sidebar); your choice is remembered between visits

## Getting Started

No setup required. Just open the file:

```bash
open index.html
```

Or serve it with any static file server:

```bash
python3 -m http.server
# then visit http://localhost:8000
```

## How to Play

1. Click any of your pieces (white moves first) — valid moves light up
2. Click a highlighted square to move
3. When a pawn reaches the back rank, choose a promotion piece from the dialog
4. The game ends automatically on checkmate or stalemate

## Project Structure

| File | Description |
|---|---|
| `index.html` | Complete application — all markup, styles, and logic |
| `rules.md` | Full chess rules reference |
| `architecture.md` | Technical design and code walkthrough |
| `specs.md` | Functional requirements and acceptance criteria |

## Browser Support

Any modern browser: Chrome 80+, Firefox 75+, Safari 14+, Edge 80+.

## License

MIT
