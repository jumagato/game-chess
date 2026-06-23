# Game Requirements & Specification

## Purpose

A browser-based chess game for beginners: complete rules enforcement, clear visual feedback, and a built-in piece guide so new players can learn without a separate reference.

## Target Users

Players who are new to chess or who want a simple, no-setup game directly in a browser. No account, no install, no internet connection required.

---

## Functional Requirements

### FR-1 Board Setup
- The board is an 8×8 grid with alternating light (`#f0d9b5`) and dark (`#b58863`) squares.
- File labels (a–h) appear below the board; rank labels (1–8) appear to the left.
- On load and on "New Game", all 32 pieces are placed in standard starting positions.

### FR-2 Turn Management
- White always moves first.
- Players alternate turns; the active turn is shown in the status bar.
- A player cannot interact with opponent pieces or move out of turn.

### FR-3 Piece Selection and Highlighting
- Clicking a piece belonging to the active player selects it (green highlight on its square).
- All legal destination squares are immediately shown:
  - Empty reachable squares: semi-transparent green dot.
  - Squares with capturable enemy pieces: red border overlay.
- Clicking anywhere other than a valid destination deselects the piece.
- Clicking a different friendly piece reselects without requiring a deselect first.

### FR-4 Move Execution
- Clicking a highlighted destination square executes the move.
- After execution the board re-renders, the last-move squares are highlighted in yellow-green, and the turn switches.

### FR-5 Legal Move Enforcement
Only moves that comply with chess rules are offered. Enforced rules:

| Rule | Enforced |
|---|---|
| Piece movement geometry (all six piece types) | Yes |
| Cannot capture own pieces | Yes |
| Cannot move into check | Yes |
| Must resolve check | Yes |
| Castling (kingside and queenside) | Yes |
| En passant | Yes |
| Pawn double-push on first move | Yes |
| Pawn promotion | Yes |

### FR-6 Castling
- Available when neither the king nor the target rook has previously moved.
- Not available when the king is in check, passes through an attacked square, or lands on an attacked square.
- Shown as a valid king destination (g-file for kingside, c-file for queenside).
- Rook teleports automatically as part of the king's move.

### FR-7 En Passant
- Generated as a legal pawn move for exactly one ply after an opponent double-pawn-push.
- The captured pawn is removed from the board when the capture is executed.

### FR-8 Pawn Promotion
- Triggered automatically when a pawn reaches the opponent's back rank.
- A modal dialog appears, requiring the player to choose: Queen, Rook, Bishop, or Knight.
- The move is not committed until a choice is made.
- The modal displays pieces in the promoting player's color.

### FR-9 Check Detection and Display
- After every move, the resulting position is checked for check.
- If the active player's king is in check:
  - The king's square turns red.
  - The status bar shows "Check!" in red alongside the player's name.

### FR-10 Checkmate Detection
- After every move, all legal moves for the opponent are enumerated.
- If the opponent has no legal moves and is in check, the game ends.
- The status bar shows "Checkmate! [Color] wins!" in gold.
- No further moves are accepted.

### FR-11 Stalemate Detection
- If the opponent has no legal moves and is not in check, the game ends as a draw.
- The status bar shows "Stalemate! It's a draw." in gold.

### FR-12 Move History
- Every executed move is recorded in standard algebraic notation (SAN).
- History is displayed in the sidebar in a two-column (white / black) layout with move numbers.
- The list auto-scrolls to the most recent move.
- Disambiguation suffixes are included when two pieces of the same type can reach the same square.
- `+` is appended for check; `#` for checkmate.
- Castling appears as `O-O` / `O-O-O`.

### FR-13 Captured Pieces
- Every captured piece is tracked and displayed in the sidebar.
- Pieces are sorted by value (Q > R > B > N > P) for easy scanning.
- Pieces are displayed as Unicode glyphs in their respective colors.

### FR-14 Undo
- The "Undo" button reverts the last move, restoring all state variables to their pre-move values (board, turn, castling rights, en passant target, captured pieces, move history).
- Undo works at any point in the game, including immediately after checkmate or stalemate.
- Repeated undo steps back through the full game history.

### FR-15 New Game
- The "New Game" button resets the entire game state to the initial position.
- All history, captures, and highlights are cleared.

### FR-16 Piece Guide
- A persistent sidebar panel lists all six piece types with their Unicode glyph and a one-sentence movement description.
- The guide is always visible and does not require interaction.

### FR-17 Sound Effects
- A short sound plays when a move is executed; a distinct sound plays when a move captures a piece (including en passant).
- Sounds are synthesized via the Web Audio API — no external audio files.
- A sidebar toggle button enables/disables sound effects; the choice persists across reloads (`localStorage`).

### FR-18 Background Music
- An ambient music loop (randomized notes from a pentatonic scale) can be toggled on/off independently of sound effects.
- A sidebar toggle button enables/disables the music; the choice persists across reloads (`localStorage`).

### FR-19 Dark/Light Mode
- A sidebar toggle button switches the page UI (background, panels, sidebar, status bar, buttons, promotion modal) between dark and light themes.
- The board's square colors, piece glyphs, and move-highlight colors are unaffected by theme — only the surrounding UI changes.
- The choice persists across reloads (`localStorage`).

---

## Non-Functional Requirements

### NFR-1 Zero Dependencies
The application has no external libraries, frameworks, or CDN resources. It runs entirely from a single `.html` file.

### NFR-2 No Server Required
The application runs directly from the filesystem (`file://`) or any static HTTP server. No backend, no API calls.

### NFR-3 Browser Compatibility
Requires a modern browser with ES2020 support (Chrome 80+, Firefox 75+, Safari 14+, Edge 80+). No polyfills provided.

### NFR-4 Responsiveness
The layout uses flexbox with `flex-wrap: wrap` so the board and sidebar stack vertically on narrow viewports.

### NFR-5 Performance
Move generation is synchronous and runs on the main thread. The approach (pseudo-legal generation + check filtering) is O(n²) per move where n is the number of pieces. This is fast enough for human play at any point in the game but is not optimized for engine-level bulk analysis.

### NFR-6 Accessibility
- Pieces are rendered as Unicode characters with a drop-shadow for legibility.
- Color is not the sole indicator of state: selected squares have both a color change and an inset border; valid moves show a geometric overlay in addition to color.
- Keyboard navigation is not currently supported.

---

## Out of Scope

The following features are intentionally excluded from this implementation:

- Computer/AI opponent
- Online multiplayer
- Clock / time controls
- Draw by threefold repetition (detection)
- Draw by insufficient material (detection)
- Automatic 50-move-rule draw declaration (clock is tracked but draw is not triggered)
- Game save / load / export to PGN
- Board orientation flip
- Piece drag-and-drop (click-to-move only)
- Keyboard move entry

---

## Acceptance Criteria

| ID | Criterion |
|---|---|
| AC-1 | Opening the file shows a correctly set-up board with all 32 pieces and "White's turn" |
| AC-2 | Clicking a white piece on white's turn shows green dot highlights on all legal squares |
| AC-3 | Clicking a highlighted empty square moves the piece; last-move squares turn yellow-green |
| AC-4 | Clicking a black piece on white's turn does nothing |
| AC-5 | A pawn on its starting rank shows two valid-move dots (one and two squares ahead) when unobstructed |
| AC-6 | A knight shows valid L-shaped destinations including those that jump over other pieces |
| AC-7 | Castling appears as a valid king move when conditions are met; rook moves automatically |
| AC-8 | En passant appears for exactly one ply after a double pawn push |
| AC-9 | Reaching the back rank with a pawn opens the promotion modal; selecting a piece completes the move |
| AC-10 | A king in check has its square highlighted red and the status bar shows "Check!" |
| AC-11 | Checkmate ends the game with the correct winner announced; no further moves are accepted |
| AC-12 | Stalemate ends the game with a draw announcement |
| AC-13 | Undo restores the exact board position, turn, and move history from before the last move |
| AC-14 | "New Game" resets everything to the initial position |
| AC-15 | Move history panel shows correct SAN including `+`, `#`, capture `x`, and castling notation |
| AC-16 | Executing a non-capturing move plays a distinct sound from a capturing move, when sound effects are enabled |
| AC-17 | Toggling sound effects off silences move/capture sounds; toggling music on/off starts/stops the ambient loop independently |
| AC-18 | Toggling dark/light mode changes the page UI colors but not the board's square or piece colors; the choice survives a page reload |
