# Raft Consensus Visual Lab

> An interactive, single-file simulator for the Raft consensus algorithm — leader election, log replication, network partitions, and crash recovery, visualized in real time.

[![Live Demo](https://img.shields.io/badge/demo-live-success?style=flat-square)](https://flynntaggart26.github.io/raft-lab/)
[![No dependencies](https://img.shields.io/badge/dependencies-none-blue?style=flat-square)](./index.html)
[![License: MIT](https://img.shields.io/badge/license-MIT-green?style=flat-square)](./LICENSE)
![HTML5](https://img.shields.io/badge/html5-single--file-orange?style=flat-square)

**[▶ Live demo — https://flynntaggart26.github.io/raft-lab/](https://flynntaggart26.github.io/raft-lab/)** · [How to demo](#-2-minute-demo-script) · [How it works](#-how-it-works) · [Run locally](#-run-locally)

---

## 📌 Overview

Distributed systems — from Google Spanner to bank ledgers — must agree on shared state even when servers crash or networks split. **Raft** (Ongaro & Ousterhout, 2014; taught in MIT 6.824 and Stanford CS244B) solves this with leader election + majority-committed log replication.

This lab makes those abstract guarantees **visible and touchable**: kill a leader mid-term, split the network 3–2, heal it — and watch safety hold.

Built as a **single self-contained `index.html`** (~11 KB, zero dependencies, no backend) so it loads instantly on GitHub Pages and is fully auditable.

## ✨ Features

| Capability | What you can do |
|------------|-----------------|
| 🗳️ **Leader election** | Randomized election timeouts (150–300 ticks), terms, majority voting, split-vote retry |
| 📝 **Log replication** | Send client writes to the leader; entries replicate via heartbeat and commit on majority ack (green) |
| ✂️ **Network partitions** | `Split 3-2`, `Isolate leader`, `Heal all` — minority stalls, majority progresses, logs converge on heal |
| 💥 **Crash recovery** | Kill/revive any node, add/remove nodes at runtime |
| ⏯️ **Time control** | Play/pause, single-step, 1–20 ticks/sec speed slider |
| 📜 **Event log** | Every election, commit, crash, and partition is timestamped by tick |

## 🎤 2-Minute Demo Script

For university presentations and interviews:

1. **Replicate** — press `Send to leader` → entry fans out to followers and commits (turns green). *Point: "Commit required 3 of 5 acks."*
2. **Kill the leader** — press `Kill leader` → timeouts fire, term increments, a new leader is elected in under a second with zero committed-data loss. *Point: "Safety under crash faults."*
3. **Partition** — press `Split 3-2` → majority side keeps committing, minority stalls. Press `Heal all` → logs converge. *Point: "A minority can never fork history — the core Raft guarantee."*

## 🧠 How It Works

Each tick of the simulation:

- **Followers** count down a randomized election timeout. On expiry they become candidates, increment the term, vote for themselves, and request votes from all *reachable* nodes.
- **Candidates** become leader on reaching a majority (`⌊n/2⌋+1`). Split votes fall back to follower with a fresh timeout.
- **Leaders** send heartbeats every 50 ticks, piggybacking any missing log suffixes, and reset follower timeouts.
- **Commit rule:** the leader advances `commitIndex` only when an entry is stored on a majority; followers learn the commit index on the next heartbeat.
- **Failure model:** `reachable(a, b)` returns false if either node is dead or they sit in different partition groups — all RPCs (RequestVote, AppendEntries) are gated through it.

This mirrors real Raft's safety argument in miniature: without a majority, nothing commits.

## 🛠️ Tech

- **Vanilla HTML / CSS / JS** — no framework, no build step, no backend, no tracking
- Deterministic tick loop (`setInterval` at `speed` Hz) with pause/step for classroom use
- Colorblind-conscious state colors; responsive grid down to phones

## 📁 Project Structure

```
raft-lab/
├── index.html   # entire app: UI + Raft state machine + renderer
└── README.md    # this file
```

## 🚀 Run Locally

No install needed — just open the file, or serve over HTTP for full fidelity:

```bash
git clone https://github.com/Flynntaggart26/raft-lab.git
cd raft-lab
python -m http.server   # → http://localhost:8000
```

Or open `index.html` directly in any modern browser.

## 🗺️ Roadmap

- [ ] Latency injection + message-drop slider (flaky network mode)
- [ ] Pre-vote phase to prevent disruptive servers
- [ ] Persistent WAL via `localStorage` with crash-replay demo
- [ ] Linearizable read (`ReadIndex`) visualization

## 📚 References

- Ongaro & Ousterhout, *In Search of an Understandable Consensus Algorithm (Raft)*, 2014 — [raft.github.io](https://raft.github.io/)
- MIT 6.824 / Stanford CS244B distributed systems curricula

## 👤 Author

Built by **Flynn Taggart** — CS applicant portfolio project exploring distributed systems through visualization.

## 📄 License

MIT — free for classroom and interview use.
