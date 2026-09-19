# Raft Consensus Visual Lab

> An interactive, single-file simulator for the Raft consensus algorithm — leader election, log replication, network partitions, and chaos engineering, visualized in real time.

[![Live Demo](https://img.shields.io/badge/demo-live-success?style=flat-square)](https://flynntaggart26.github.io/raft-lab/)
[![No dependencies](https://img.shields.io/badge/dependencies-none-blue?style=flat-square)](./index.html)
[![License: MIT](https://img.shields.io/badge/license-MIT-green?style=flat-square)](./LICENSE)
![Single file](https://img.shields.io/badge/html5-single--file-orange?style=flat-square)

**[▶ Live demo — https://flynntaggart26.github.io/raft-lab/](https://flynntaggart26.github.io/raft-lab/)** · [Demo script](#-2-minute-demo-script) · [Features](#-features-v2) · [How it works](#-how-it-works) · [Run locally](#-run-locally)

![Raft Lab topology](https://img.shields.io/badge/UI-glass--dark-light--mode-blueviolet?style=flat-square)

---

## 📌 Overview

Distributed systems — from Google Spanner to bank ledgers — must agree on shared state even when servers crash or networks split. **Raft** (Ongaro & Ousterhout, 2014; taught in MIT 6.824 and Stanford CS244B) solves this with leader election + majority-committed log replication.

This lab makes those guarantees **visible and touchable**: kill a leader mid-term, inject packet loss and latency, split the network 3–2, heal it — and watch safety hold, with live metrics to prove it.

Built as a **single self-contained `index.html`** (~26 KB, zero dependencies, no backend) so it loads instantly on GitHub Pages and is fully auditable.

## ✨ Features (v2)

| # | Feature | What improved |
|---|---------|---------------|
| 1 | 🌐 **Live topology map** | Ring layout with animated packets (green = replication, purple = heartbeat, yellow = votes, red ✕ = dropped), 👑 leader crown, pulse ring, shaded A/B partition zones, hover tooltip per node (state · term · commit) |
| 2 | 📊 **Metrics dashboard** | Commits, elections, throughput with live **sparkline**, avg commit latency (ticks), majority availability %, tick counter |
| 3 | 🎬 **Narrated scenarios** | One-click guided tours — Replicate / Kill leader / Split-brain / Full tour — with step-by-step narration banner, Next/Stop controls |
| 4 | 🌩️ **Chaos tuning + WAL** | Packet-loss % slider, **latency slider** (delayed-delivery queue), drop counter, WAL persistence toggle, **View WAL modal** + one-click **replay test** proving no committed entry is lost |
| 5 | 💎 **Glass UI + sharing** | Light/dark theme, term-colored commit timeline, filterable event log, **Copy share link** (encodes nodes/loss/latency), **Export event log CSV**, keyboard shortcuts (`space` play/pause, `s` step, `w` write, `h` heal) |

Core Raft behavior: randomized election timeouts (150–300 ticks), 50-tick heartbeats, majority commit (`⌊n/2⌋+1`), split-vote retry, per-node kill/revive, add/remove nodes, 1–20 ticks/sec speed + single-step mode.

## 🎤 2-Minute Demo Script

For university presentations and interviews — press **▶ Scenario: full tour** and narrate:

1. **Replicate** — client write fans out (green packets) and commits on 3/5 acks. *Point at sparkline + timeline.*
2. **Kill the leader** — timeouts fire, yellow votes, new term, new leader, zero committed-data loss. *Point at WAL replay test.*
3. **Split-brain 3–2** — majority side keeps committing, minority stalls (red dashed links). **Heal** → logs converge. *Closing line: "A minority can never fork history — the core Raft guarantee."*

## 🧠 How It Works

Each simulation tick:

- **Followers** count down a randomized election timeout. On expiry they become candidates, increment the term, vote for themselves, and request votes from all *reachable* nodes.
- **Candidates** become leader on reaching a majority. Split votes fall back to follower with a fresh timeout.
- **Leaders** send heartbeats every 50 ticks, piggybacking missing log suffixes (optionally delayed by the latency queue), resetting follower timeouts.
- **Commit rule:** the leader advances `commitIndex` only when an entry is stored on a majority of the *same partition*; followers learn it on the next heartbeat.
- **Failure model:** every message passes through `reachable()` + loss roll + partition check. Dropped packets render as red ✕; delayed ones sit in `delayQ`. This mirrors real Raft's safety argument: without a majority, nothing commits.

## 🛠️ Tech

- **Vanilla HTML / CSS / JS + Canvas** — no framework, no build step, no backend, no tracking
- Deterministic tick loop (`setInterval` at `speed` Hz) with pause/step for classroom use
- `localStorage` WAL for the committed prefix; URL params for shareable configs

## 📁 Project Structure

```
raft-lab/
├── index.html   # entire app: UI + Raft state machine + Canvas renderer
└── README.md    # this file
```

## 🚀 Run Locally

```bash
git clone https://github.com/Flynntaggart26/raft-lab.git
cd raft-lab
python -m http.server   # → http://localhost:8000
```

Or open `index.html` directly in any modern browser.

## 🗺️ Roadmap

- [x] Latency injection + packet-loss chaos
- [x] Persistent WAL with replay test
- [x] Narrated scenario tours + share links
- [ ] Pre-vote phase to prevent disruptive servers
- [ ] Linearizable read (`ReadIndex`) visualization
- [ ] Joint-consensus membership change animation

## 📚 References

- Ongaro & Ousterhout, *In Search of an Understandable Consensus Algorithm (Raft)*, 2014 — [raft.github.io](https://raft.github.io/)
- MIT 6.824 / Stanford CS244B distributed systems curricula

## 👤 Author

Built by **Flynn Taggart** — CS applicant portfolio project exploring distributed systems through visualization.

## 📄 License

MIT — free for classroom and interview use.
