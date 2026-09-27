# Changelog

## 5b3 - recommended for S950 users
- **Dropped Data Encoder movement sent via MIDI (S950)** - it had to go, to make room for other fixes in tightly packed codebase, S900 retains this feature
- **Sample corruption fixes (S950)** — fixed two cases where samples would be append with noise at the end, only S950 build was affected
- **Sample Start fixes (S950)** — now covers the whole multi-segment sample, only S950 was affected by this bug
- **CC values clamped to range (S950)** — CC values on keygroup parameters are now clamped to each field's own min/max, S900 already handles this properly
- **Minor cosmetic fixes/changes** — fixed truncated message (S900), MIDI SYNC renamed to MIDI POLY (S900), Start is now Smpl Start in CC Map.

## 5b2

- **Madlabz OS ported to S950** — it retains majority of the newly added functionality 
- **Restructured CC MAP to be identical on both machines** — S900 retains the VX only fields, while S950 added **One-shot** and **LFO Desync** keygroup switches 
- **Madlabz OS Settings persistence improvements** — SAVE/LOAD ALL will write MADLABZ OS settings object on the disk instead. It now cross-loads between an S900 and an S950.
- **Few minor UI tweaks** — mostly clean up and few optimizations, etc.
- **Mistakes prevention (S900)** - Double-confirm on destructive actions and time-stretch will explain with message why it refused
- **Graceful fault recovery** — OS fault will attempt to return you to menu instead of freezing
- **Segment-pool overflow guard (S950)** — a sample-chain build that would overrun the replay pool refuses with "Full. Erase something" instead of corrupting.
- **RS232 removed (S900)** - MIDI pages renumber to match the S950

## 5b1

First public beta — the initial MADLABZ OS release.
