# Game Architecture

## Overview

A single-file, zero-dependency browser application (`index.html`). All HTML, CSS, and JavaScript live in one file — no build step, no server required. Open the file directly in any modern browser to play.

## File Structure

```
game-chess/
└── index.html   — complete application (markup + styles + logic)
```

## Technology Choices

| Concern | Choice | Reason |
|---|---|---|
| Runtime | Vanilla JS (ES2020) | No dependencies, instant load, works offline |
| Rendering | DOM manipulation | Simple for a grid-based game; no canvas needed |
| Pieces | Unicode chess symbols | No image assets required |
| Styles | Inline CSS | Keeps the app self-contained |
| State persistence | In-memory only | Scope of a single session is sufficient |

## State Model

All game state is held in module-level variables:

| Variable | Type | Description |
|---|---|---|
| `board` | `string[][]` (8×8) | Each cell is `null` or a two-char piece code (`wK`, `bP`, …) |
| `turn` | `'w'` or `'b'` | Whose turn it is |
| `selected` | `[row, col]` or `null` | Currently clicked square |
| `validMoves` | `Move[]` | Legal destinations for the selected piece |
| `lastMove` | `{from, to}` or `null` | Used to highlight the previous move |
| `history` | `Snapshot[]` | Full board snapshots saved before each move; powers undo |
| `capturedPieces` | `string[]` | All pieces taken, in order of capture |
| `castlingRights` | `{wK, wQ, bK, bQ}` | Whether each side can still castle kingside/queenside |
| `enPassantTarget` | `[row, col]` or `null` | Square where en passant capture lands (valid for one ply only) |
| `halfMoveClock` | `number` | Counts half-moves since last pawn move or capture (50-move rule) |
| `gameOver` | `false` or `{type, winner?}` | Set after checkmate or stalemate |

### Piece encoding

Pieces are two-character strings: color prefix (`w`/`b`) + type letter (`K Q R B N P`).  
Example: `'wQ'` = white queen, `'bP'` = black pawn.

## Core Logic Pipeline

```
User click
    │
    ▼
handleSquareClick(r, c)
    │
    ├─ If a piece is selected and the click is a legal destination
    │       │
    │       ├─ promo flag? → showPromoModal() → executeMove(move, piece)
    │       └─ otherwise  → executeMove(move, null)
    │
    └─ Otherwise → select the piece and compute getLegalMoves()
```

### Move generation

Two-stage pipeline separates pseudo-legal from legal moves:

1. **`getRawMoves(board, r, c, castlingRights, enPassantTarget)`**  
   Generates all geometrically possible moves for a piece, including special moves (castling, en passant, double pawn push, promotion). Does not check whether the moving side's king is left in check.

2. **`getLegalMoves(board, r, c, castlingRights, enPassantTarget)`**  
   Filters `getRawMoves` output by simulating each candidate move with `applyMove` and calling `isInCheck` on the result. Only moves that leave the king safe are returned.

3. **`getAllLegalMoves(board, color, castlingRights, enPassantTarget)`**  
   Aggregates legal moves for every piece of a given color. Used to detect checkmate and stalemate (empty result = no legal moves).

### Check detection

`isSquareAttacked(board, r, c, byColor)` iterates every opponent piece and calls `getRawMoves` on each. If any raw move lands on `(r, c)`, the square is attacked. This is used by:
- `isInCheck` — checks whether a color's king square is attacked.
- Castling validation — the king must not pass through attacked squares.
- `getLegalMoves` — filters moves that would leave the king in check.

### Applying moves

`applyMove(board, move, promoPiece)` returns a new board (immutable — deep-copies the 2D array) with the move applied, handling:
- Normal piece movement
- Rook teleport on castling
- En passant pawn removal
- Pawn promotion replacement

### Undo

Before `executeMove` mutates any state, it pushes a `Snapshot` onto `history` — a copy of every mutable variable. `undoMove` pops the last snapshot and restores all variables from it. This approach is simple but trades memory for simplicity; for very long games each snapshot holds a full 8×8 board copy.

## Rendering

Rendering is fully synchronous and re-runs from scratch on every state change (no virtual DOM, no diffing).

**`renderBoard()`** — clears `#board` and rebuilds 64 `<div class="square">` elements. CSS classes applied per-square:

| Class | Condition |
|---|---|
| `light` / `dark` | `(row + col) % 2 === 0` |
| `selected` | Square equals `selected` |
| `valid-move` | Square is in `validMoves` and is not a capture |
| `valid-capture` | Square is in `validMoves` and holds an enemy piece (or en passant) |
| `last-move` | Square was the source or destination of `lastMove` |
| `in-check` | Square holds the king of the current player who is in check |

**`renderSidebar()`** — rebuilds the move history list (3-column grid: number, white's move, black's move) and the captured pieces display (sorted by piece value).

**`updateStatus()`** — updates `#status-text` to reflect whose turn it is, whether that player is in check, or the game-over message.

## Algebraic Notation

`toAlgebraic(board, move, allMoves, promoPiece)` produces standard short algebraic notation (SAN):
- Piece letter prefix for non-pawns.
- Disambiguation suffix (file, rank, or both) when two pieces of the same type can reach the same square.
- `x` for captures, `=` for promotion, `O-O` / `O-O-O` for castling.
- `+` and `#` suffixes are appended by `executeMove` after the resulting position is evaluated.

## CSS Architecture

All styles are in a single `<style>` block. Layout uses:
- `flexbox` for the overall page and sidebar.
- CSS `grid` (8×8) for the board squares.
- CSS `::after` pseudo-elements for move-hint dots and capture rings (no extra DOM nodes).
- `!important` overrides on dynamic state classes (`selected`, `in-check`, `last-move`) to guarantee they always win over the base light/dark color.

## Promotion Modal

A `position: fixed` overlay (`#promo-modal`) is hidden by default (`display: none`) and shown by toggling the `.show` class. Promotion choice buttons are built dynamically from the moving player's color so the correct colored piece glyphs are displayed.
