# Tiny Chess Engine

A compact chess engine in a single Python file (`main.py`, 4,093 bytes) for
the [make.fun tiny-chess-01 bounty](https://make.fun/bounty/tiny-chess-01).

It plays the full gauntlet protocol: it reads the environment URL from
`MAKEFUN_ENV_URL`, plays UCI moves against the house engines, and answers
well within the 10-second move limit (about 2–3 s per move at its fixed
node budget).

## How it works

- **Board:** a 0x88 mailbox array with incremental make/unmake and Zobrist
  keys, so the search runs at about 20,000 nodes per second in pure Python.
- **Search:** iterative-deepening alpha-beta with a **fixed node budget**
  (60,000 nodes per move, so every trial replays deterministically — no wall
  clock, no unseeded randomness), a transposition table, null-move pruning,
  late-move reductions with re-search, a killer move and a history heuristic
  for move ordering, and a quiescence search over captures, promotions and
  replies to check. Repetition and the 50-move rule score as draws.
- **Evaluation:** material (pawn 100, knight 320, bishop 330, rook 500, queen
  900) plus piece-square terms, pawn advancement and centralisation, bishop
  pair, rook on the 7th rank, and king centralisation only in a true
  endgame. The search usually reaches depth 4, with quiescence beyond that.
- **Size:** the readable source is minified and packed as an lzma payload
  (the unpacker is the whole of `main.py`), so the engine fits the 4,096
  byte limit. The same source always builds the same file.

## How to run the practice trials

The kit's practice instances live in `practice/trial-0|1|2`. The local
grading harness drives the kit's own environment server and scorer; the
engine is run as `python3 -P -s main.py` with `MAKEFUN_ENV_URL` set to the
local server. A single practice trial (6 games) takes about 8 minutes.

## Practice results

Measured with the kit's local grading harness on the three practice trials
(6 games each, opponents depth-2 through nodes-500k):

| trial | Elo | W–D–L |
|-------|-----|-------|
| 0 | 1364 | 3–1–2 |
| 1 | 1191 | 2–1–3 |
| 2 | 1279 | 3–0–3 |
| **mean** | **1278** | **8–2–8** |

The house baseline (sunfish, 50,000 nodes/move) rates about 1,325 mean on
the same practice trials.

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/herusetiawan27/tiny-chess-engine/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/herusetiawan27/tiny-chess-engine/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

## License

MIT — see `LICENSE`.
