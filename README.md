# Raft Consensus Visual Lab

> Interactive Raft simulator: leader election, log replication, network partitions, crash recovery — in one file, no backend.

**Live demo:** open `index.html` or serve with `python -m http.server` → http://localhost:8000

## Why this matters (use in your presentation)

Distributed systems power everything (Google, banks, airplanes) but fail in subtle ways. Raft (MIT 6.824 / Stanford CS244B) solves consensus: how do 5 servers agree even when some crash or the network splits? This lab makes that visible.

## Features

- **Leader election** with randomized timeouts, terms, voting
- **Log replication** — send client writes, watch majority commit (green)
- **Partitions:** Split 3-2, Isolate leader, Heal — minority stalls, majority progresses, logs converge
- **Crash/recover** any node, add/remove nodes, speed control, step mode
- Event log + commit index per node

## 2-minute demo script

1. `Send to leader` → log replicates, commits.
2. `Kill leader` → timeouts fire, new term, new leader in <1s, no committed data lost.
3. `Split 3-2` → majority side commits, minority stalls. `Heal all` → logs converge.

Say: "Same guarantee as real Raft: commit needs a majority, so a minority partition can't fork history."

## Tech

Vanilla HTML/CSS/JS, no deps. State machine ticks at `speed` Hz. `reachable(a,b)` gates all messages by liveness + partition group.

## Limitations / next

- No persistence (WAL), no joint-consensus membership change — votes are instant in-tick
- Next: latency injection, pre-vote, linearizable read demo

## Run

```bash
cd raft-lab
python -m http.server
```
