# MADLABZ OS 5.0 for the AKAI S900 and S950

A new operating system for the AKAI **S900** and **S950** adding ton of new features whiles maintaining backward-compatibility with stock OS. Boots from a floppy disk/image. Available Sample RAM is the same as stock!

**Note:** Currently in public beta. The final set of features may be reduced due to very limited space. To report issues go to [GitHub Issues](../../issues).
## Download

Firmware images are published on the [Releases](../../releases) page:

- `s900-madlabz-os.img` — for the AKAI **S900**
- `s950-madlabz-os.img` — for the AKAI **S950**

## Running on Hardware

Each image is a raw 819,200-byte Akai floppy disk. You can use them directly with Gotek / HxC floppy emulator. Use Greaseweazel to write image to a 3.5" DD floppy disk. Standard USB floppy disk won't write disk with non-standard sector layout.

To load **S950** version, boot from stock OS, insert disk and go to `DISK *02` page and move cursor to `Clear mem and load disk (1)` and press `1`. **S900** loads normally on power-on.


---

## Key features added vs stock OS (S900 4.0 / S950 1.2b):

- **TIME STRETCH** (S900 `EDIT SAMPLE *16` / S950 `EDIT SAMPLE *14`) — native to the S950, ported to the S900. Your wallet will thank you!
- **FILTER TRACKING/BYPASS modes** (`UTILITY *01`) — with stock/+1oct/+2oct tracking, disabled filter tracking and anti-alias filter bypass (16 kHz/20 kHz). 
- **Support up to 43 samples (S900)** — Up to 32 samples behaves like stock. Going beyond needs 2 samples per keygroup (soft/loud). Max 43 samples + 22 keygroups + 1 program (shared one 66-slot directory). An older OS still loads the disk but only its first 32 samples
- **Segment-pool overflow guard (S950)** — a sample-chain build that would overrun the replay pool refuses with "Full. Erase something" instead of corrupting samples
- **Sample start via CC** — controllable note start, so a single sample slot can live-chop breaks or do other tricks!
- **Assignable CC MAP** (`MIDI *05`) — 33 parameters gain their dedicated CC — amp/filter envelopes, LFO, filter cutoff, velocity, loudness, warp, sample start, pitch, glide, etc. (full list below). Both S900 and S950 have unique CC parameters but the map is identical on both machines to allow cross-loading.
- **Data Encoder movement sent via MIDI**  — Move cursor to parameter field, and move the encoder to send CC values (see CC Map for supported fields)
- **Parameter receive via MIDI** — Not only receives all CC transmitted but also updates the screen value for any currently selected parameter (see CC Map for supported fields)
- **MIDI SYNC (S900)** (`MIDI *06`) — pair two S900s on one MIDI stream for double the polyphony (Round Robin, Odd/Even)
- **Zero-crossing snap** (`EDIT SAMPLE *06`/`*07`/`*08`) — ENT snaps a start/end/loop point to the nearest zero crossing within ±64 samples, for click-free edits
- **Quiet-tail trim (S900)** (`EDIT SAMPLE *07`) — Added Gate parameter with threshold to trim silence at the end. 
- **Improved disk operations** — raised retry count and verified writes. For S900 also adds faster multi-block program loads. 
- **MADLABZ OS settings persistence** — SAVE/LOAD ALL will also store/load the CC map plus newly added global parameters (Filter Tracking/Bypass mode, MIDI Sync mode, etc.). Stock OS will ignore this data
- **Live filter cutoff** — the filter cutoff follows a MIDI CC in real time, moving notes that are already sounding as well as the next ones.
- **Prompt user before destructive actions** **(S900)**: user is prompted when attepting any of the destructive actions

## Stock features adjusted or removed:

**Madlabz OS vs Stock OS (S900 4.0 / S950 1.2b):**
- **Service hex monitor** — removed, after experiencing OS fault it will attempt to return to the menu instead of freezing. Stock would open service hex monitor (0xDEADC0DE)
- **RS232 serial** — removed, MIDI control is untouched
- **Drum trigger inputs (S900)**  — rare as hen's teeth; if you're (un)lucky enough to have one, use the stock OS
- **Compressed sample saving (S900)** — dedicated DISK page removed, saves are uncompressed; compressed samples still load
- **ME35T drum-machine editor (S950)** — removed alongside its dedicated pages (`UTILITY`, `DISK`). Use stock OS if you need this functionality.
- **Memory full variants unification (S950)**  - cosmetic, saves space while retaining functionality
- **HD Volume select via Program Change (S950)** — the on/off switch moved to `MIDI *04` instead of dedicated page
- **AUTO on `EDIT SAMPLE *06` was renamed to GATE** — cosmetic, for consistency with newly added Quiet-tail trim and to avoid confusion what `AUTO` does next to `0-snap`


---

## FAQ

In case you have questions:
- **Can you add another feature?** — I wish but there is almost no space left. This sentence alone wouldn't fit in the leftover space!
- **Why the stock features were removed?** — MADLABZ OS has to fit in exactly the same space as the stock firmware. The key objective was to retain the same sample RAM region as stock, so compromises had to be made.
- **Why S900 has more features than S950?**  — Stock OS for S950 already has much more densely packed code (SCSI, S1000 disk support).
- **Why S950 doesn't boot from OS disk?** — A bug in stock ROM code prevents it. Future Madlabz OS version will offer binary files you can burn to EPROMs and replace stock OS ROMS.

---

## Documentation

- User manual — soon
- [Changelog](CHANGELOG.md) — version history
