# Chess Rules

## Objective

Checkmate the opponent's king — put it under attack with no legal escape.

## The Board

An 8×8 grid of alternating light and dark squares. Files (columns) are labeled a–h left to right; ranks (rows) are labeled 1–8 bottom to top. White always starts at ranks 1–2, black at ranks 7–8.

## Pieces and How They Move

### King (♔ / ♚)
Moves one square in any direction (horizontal, vertical, or diagonal). Cannot move into check.

### Queen (♕ / ♛)
Moves any number of squares in any direction — horizontal, vertical, or diagonal. Cannot jump over pieces.

### Rook (♖ / ♜)
Moves any number of squares horizontally or vertically. Cannot jump over pieces.

### Bishop (♗ / ♝)
Moves any number of squares diagonally. Always stays on its starting color. Cannot jump over pieces.

### Knight (♘ / ♞)
Moves in an L-shape: two squares in one direction then one square perpendicular (or vice versa). The only piece that can jump over other pieces.

### Pawn (♙ / ♟)
- Moves one square forward (toward the opponent's side).
- On its first move only, may advance two squares forward.
- Captures one square diagonally forward — pawns cannot capture straight ahead.

## Special Moves

### Castling
The king and a rook swap in a single move. Requirements:
- Neither the king nor the chosen rook has previously moved.
- No pieces stand between the king and rook.
- The king is not currently in check.
- The king does not pass through or land on a square that is under attack.

Kingside castling: king moves two squares toward the h-file rook (g1/g8); rook jumps to f1/f8.  
Queenside castling: king moves two squares toward the a-file rook (c1/c8); rook jumps to d1/d8.

### En Passant
When a pawn advances two squares from its starting rank and lands beside an opponent's pawn, the opponent may capture it as if it had moved only one square. This capture must be made immediately on the very next move or the right is lost.

### Pawn Promotion
When a pawn reaches the opponent's back rank (rank 8 for white, rank 1 for black), it must be replaced by a queen, rook, bishop, or knight of the same color. Promotion to queen is most common.

## Check, Checkmate, and Stalemate

**Check** — the king is under direct attack. The player in check must immediately resolve it by moving the king, blocking the attack, or capturing the attacker.

**Checkmate** — the king is in check and there is no legal move to escape. The player in checkmate loses the game.

**Stalemate** — the player whose turn it is has no legal move but is not in check. The game ends in a draw.

## Draws

A game may also end in a draw by:
- **50-move rule** — fifty consecutive moves by each player with no pawn move and no capture.
- **Threefold repetition** — the same position occurs three times with the same player to move.
- **Insufficient material** — neither player has enough pieces to force checkmate (e.g., king vs. king).
- **Agreement** — both players agree to a draw.

> This implementation detects checkmate and stalemate automatically. The 50-move rule clock is tracked internally (`halfMoveClock`) but a draw is not yet declared automatically; other draw conditions are not currently enforced.

## Turn Order

White always moves first. Players alternate turns. A player must make exactly one move per turn.

## Winning

The game ends the moment checkmate is delivered. There is no need to capture the king — the game stops as soon as no legal escape exists.
