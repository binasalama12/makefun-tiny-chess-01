# tiny-chess-01 entry

An original chess engine in 4 KB of Python. `main.py` is the whole
engine: an lzma + base85 payload with a two-line unpacker (the rules
allow this format explicitly; payload and unpacker together are
4,076 bytes). Unpacked, the engine is a single stdlib-only module:

- 0x88 board, incremental Zobrist hashing, incremental material +
  piece-square evaluation (formula PSTs, bishop pair, king
  middlegame/endgame blend on non-pawn material).
- Negamax alpha-beta with principal variation search, null-move
  pruning, late move reductions, check extensions, and a quiescence
  search (captures, promotions, check evasions) folded into the main
  search; transposition table with exact/lower/upper bounds.
- Move ordering: TT move, MVV-LVA captures, killer moves.
- Fully deterministic: no clock, no unseeded randomness. Iterative
  deepening under a fixed node budget that depends only on the plies
  played so far (45k nodes before ply 100, 25k before ply 150, 12k
  after), so every replay of a position reproduces the same move and
  the worst-case thinking time over an 8-game trial stays bounded.
- Draw handling mirrors the env: threefold and fifty-move positions
  score 0 in search; insufficient material returns no move (the env
  ends the game first).

Verified locally: perft matches known node counts on six standard
positions, root legal-move sets match python-chess, full games
against the kit starter and a random mover complete with every move
legal, and local trials against the kit's real environment (the
practice gauntlet) score in the same band as the house 4 KB baseline.
The payload was later re-packed with a stronger lzma preset (9,
extreme); the unpacked engine source is byte-identical, and repeat
stub matches reproduce the original engine's games move-for-move.

## License

MIT — see `LICENSE`.
