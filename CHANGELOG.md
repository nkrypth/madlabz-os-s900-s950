# Changelog

## 5b2

- **Madlabz OS ported to S950** — it retains majority of the newly added functionality 
- **Restructured CC MAP to be identical on both machines** — S900 retains the VX only fields, while S950 added **One-shot** and **LFO Desync** keygroup switches 
- **Madlabz OS Settings persistence improvements** — SAVE/LOAD ALL will write MADLABZ OS settings object on the disk instead. It now cross-loads between an S900 and an S950.
- **Few minor UI tweaks** — mostly clean up and few optimizations, etc.
- **Mistakes prevention (S900)** - Double-confirm on destructive actions
- **Graceful fault recovery** — OS fault will attempt to return you to menu instead of freezing
- **Segment-pool overflow guard (S950)** — a sample-chain build that would overrun the replay pool refuses with "Full. Erase something" instead of corrupting.
- **RS232 removed (S900)** - MIDI pages renumber to match the S950

## 5b1

First public beta — the initial MADLABZ OS release.
