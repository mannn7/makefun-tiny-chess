# makefun-tiny-chess

A 4 KB chess engine for make.fun's tiny-chess-01: build the strongest chess engine in 4,096 bytes of Python.

[![tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/mannn7/makefun-tiny-chess/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/mannn7/makefun-tiny-chess/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

## The engine

`main.py` (2,952 bytes) is the entrypoint packed by the kit's `makefun compress` (LZMA): it unpacks and execs the real engine, a 0x88-board negamax search with:

- PeSTO piece-square tables (midgame king table, endgame king table when no queens are left) and a bishop-pair bonus
- Alpha-beta with principal variation search, transposition table, quiescence search, null-move pruning and killer moves
- Game-history repetition awareness: positions already seen in the real game count toward the threefold check, so the engine does not throw away wins by shuffling
- A fixed budget of 75,000 nodes per move — no wall clock, no randomness, fully deterministic

## Practice results

Graded locally with the kit's own env server and scorer (three practice trials, 6 games each):

| trial | Elo | W | D | L | spare plies |
|---|---:|---:|---:|---:|---:|
| trial-0 | 1364 | 3 | 1 | 2 | 239 |
| trial-1 | 1191 | 1 | 3 | 2 | 105 |
| trial-2 | 1279 | 3 | 0 | 3 | 405 |

Mean practice Elo is about 1,278, above the 1,154 floor for the final.

## How it talks to the env

The program reads `MAKEFUN_ENV_URL`, plays through `POST /game` and `POST /move`, and always answers with a legal UCI move from the env's own legal-move list. It imports nothing beyond the standard library.
