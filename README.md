# makefun-tiny-chess

A 4 KB chess engine for make.fun's tiny-chess-01: build the strongest chess engine in 4,096 bytes of Python.

[![tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/mannn7/makefun-tiny-chess/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/mannn7/makefun-tiny-chess/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

## The engine

`main.py` (3,015 bytes) is the entrypoint packed by the kit's `makefun compress` (LZMA): it unpacks and execs the real engine, a 0x88-board negamax search with:

- PeSTO piece-square tables (midgame king table, endgame king table when no queens are left) and a bishop-pair bonus
- Alpha-beta with principal variation search, transposition table, quiescence search, null-move pruning and killer moves
- Aspiration windows at the root: after the first depth, the window narrows around the previous score and re-searches wide only on a fail low/high
- Game-history repetition awareness: a position repeated once in the real game is already scored as a small loss when not losing (contempt −30), so the engine plays on for the win instead of settling for a draw
- A fixed budget of 75,000 nodes per move — no wall clock, no randomness, fully deterministic

## Practice results

Graded locally with the kit's own env server and scorer (three practice trials, 6 games each):

| trial | Elo | W | D | L | spare plies |
|---|---:|---:|---:|---:|---:|
| trial-0 | 1451 | 3 | 2 | 1 | 245 |
| trial-1 | 1451 | 3 | 2 | 1 | 251 |
| trial-2 | 1279 | 3 | 0 | 3 | 331 |

Mean practice Elo is about 1,394, above the 1,154 floor for the final and above the house baseline's 1,365.

## How it talks to the env

The program reads `MAKEFUN_ENV_URL`, plays through `POST /game` and `POST /move`, and always answers with a legal UCI move from the env's own legal-move list. It imports nothing beyond the standard library.
