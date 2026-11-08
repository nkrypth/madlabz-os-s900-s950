# Changelog

## 5b4

Three disk images now: **S950 SCSI** (Akai disks only), **S950 FAT** and **S900 FAT** (Akai + FAT12 floppies). See the README for which to pick.

**New**
- **Virtual sample slicing** (`EDIT SAMPLE *05`) — slice a sample or loop into up to 64 independently playable slices, with no extra memory used
- **FAT12 / WAV support** (S950 FAT and S900 FAT) — read and write PC-formatted floppies and import 8/16-bit mono WAVs up to 48 kHz; saves are native Akai `.S9` / `.P9` files
- **Bigger S900 directory** — 98 objects instead of 66: up to 96 samples, 64 with loud + soft per keygroup, 48 with one sample per keygroup; programs up to 72 keygroups; multi-disk loading
- **Tempo-sync LFO** — the keygroup LFO locks to incoming MIDI clock on musical divisions (set by the stock LFO Rate)
- **Filter-cutoff LFO** (`EDIT PROGRAM *14`, S900 `*13`) — a second, per-keygroup LFO destination: the filter cutoff
- **Sample start / end / pitch over MIDI** — a movable live play window and a wide transpose (5b3 had sample start only)
- **MIDI learn** — pick a keygroup and set key ranges by playing
- **Portamento / glide** (S950 `EDIT PROGRAM *12`, S900 `*11`)
- **VCF instant attack** — `*Attk 0` is a true instant attack
- **MIDI Sample Dump Standard (S950)** — SDS send and receive work, byte-identical to stock 1.2b
- **UTILITY info page** — the UTILITY key shows the Madlabz OS version banner

**Changed**
- **S900 rebuilt on the stock OS 4.0 code** — the S900 build now carries the stock 4.0 OS with the Madlabz features added
- **Filter tracking / bypass is per keygroup** (`EDIT PROGRAM *04 *FltrTrack`) instead of one global setting on `UTILITY *01`
- **CC map is fixed** — the 33 CC parameters are the same on both machines; the assignable editor page (`MIDI *05`) is gone
- **Data Encoder MIDI send is back on the S950** (5b3 had dropped it)
- **Zero-crossing snap, Quiet-tail trim and Mistake prevention** now on both machines
- **Time Stretch** is `EDIT SAMPLE *14` on both machines (S900 was `*16`)
- **Disk verify-after-write** on both machines; **faster program loads** (S900)
- **Segment-pool exhaustion fix (S950)** — replaces 5b3's overflow guard: when the playback pool runs out, the sample plays truncated instead of reading past its memory
- **OMNI off by default**, and the memory-full messages are unified into `Full. Erase something` on both machines

**Removed**
- **MIDI SYNC / POLY (S900)**
- **Madlabz OS settings object** on SAVE / LOAD ALL (the CC map is fixed, so there is nothing to save)
- **Digital input recording (S950)** — its room funds MIDI SDS
- **Velocity crossfade, ensembles and compressed samples (S900)** — OS 4.0-only features

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
