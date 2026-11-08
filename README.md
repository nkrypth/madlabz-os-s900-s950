# MADLABZ OS 5.0 for the AKAI S900 and S950

A new operating system for the AKAI **S900** and **S950** adding ton of new features whiles maintaining backward-compatibility with stock OS. Boots from a floppy disk/image. Available Sample RAM is the same as stock!

**Note:** Currently in public beta. The final set of features may be reduced due to very limited space. To report issues go to [GitHub Issues](../../issues).

---

## AI Disclaimer

If you doesn't want to touch anything created with AI with a 10 foot pole then skip reading now. AI was used in this project to reverse engineer stock OS and help develop new functionality. However, all design & architecture decisions were mine. If there are bugs in the code then it's on me because I didn't test it enough or think through the design carefuly. 

---

## Download

Firmware images are published on the [Releases](../../releases) page:

| Madlabz OS disk image    | Sampler | Floppy | SCSI | Disk Format  |
| ------------------------ | ------- | ------ | ---- | ------------ |
| s950-scsi-madlabz-os.img | S950    | DD+HD  | Yes  | Akai only    |
| s950-fat-madlabz-os.img  | S950    | DD+HD  | No   | Akai + FAT12 |
| s900-fat-madlabz-os.img  | S900    | DD     | No   | Akai + FAT12 |

---

## Running on Hardware

The firmware ships as raw Akai double-density floppy images. Except for SCSI/FAT support (S950) the the set of features is identical. Available Sample RAM is the same as stock! Which version you're currently running can be identified by boot banner (`AKAI S950 SCSI`, `AKAI S950 FAT`, `AKAI S900 FAT`).

You can run an image two ways:

- **Floppy emulator (FlashFloppy/ HxC).** Drop the disk image on the USB stick and adjust config file to boot it
- **Real floppy.** Write the image to a 3.5" DD disk with a Greaseweazle. A standard USB floppy drive
  cannot write this non-standard Akai sector layout.

---

## Key features added vs stock OS (S900 4.0 / S950 1.2b):

- **Time Stretch** ( `EDIT SAMPLE *14`) — native to the S950, ported to the S900. Your wallet will thank you!
- **Virtual sample slicing** (`EDIT SAMPLE *05`) — slice your larger samples/loops into independently playable slices up to 64 per sample, with no extra memory used.
- **Filter tracking / bypass** (`EDIT PROGRAM *04`)— a per-keygroup `*FltrTrack` mode with stock/+1oct/+2oct tracking, disabled filter tracking and anti-alias filter bypass (16 kHz/20 kHz). 
- **FAT12 / WAV support** — read and write PC-formatted floppies, import 8/16bit up to 48khz WAV files
- **Bigger S900 directory and multi-disk loading** (S900) — 98 directory objects instead of stock 66, allows up to 96 unassigned samples, 64 samples when using 2 per keygroup (loud and soft) or 48 samples/keygroup.
- **Tempo-sync LFO** (LFO Rate on `EDIT PROGRAM *13`, S900 `*12`) — the keygroup LFO locks to incoming MIDI clock and cycles on musical beat divisions.
- **Filter-cutoff LFO**  (`EDIT PROGRAM *14`, S900 `*13`) — a second LFO destination assigned per-keygroup
- **Added 33 new CC parameters** — amp/filter envelopes, LFO, cutoff, velocity response, loudness, portamento, and more gain their dedicated CC
- **Data Encoder MIDI send** — editing a mapped parameter on the panel transmits its control change on the   Basic channel, so a DAW records your moves. 
- **Sample start / end / pitch over MIDI** — a movable live play window and a wide transpose.
- **Live filter cutoff over MIDI** — the filter cutoff follows CC 74 in real time, moving notes   already sounding.
- **MIDI learn** — pick a keygroup by ear, and set key ranges by playing, instead of dialling numbers.
- **Portamento / glide** (S900`EDIT PROGRAM *11` / S950 and `*12`)  — a dedicated note-to-note pitch glide
- **Zero-crossing snap** (`EDIT SAMPLE *06` / `*07` / `*08`) — `*0Snap` a start/end/loop point to the nearest zero crossing for click-free edits.
- **Quiet-tail trim** (`EDIT SAMPLE *07`) — added `*Gate` parameter with threshold to trim silence off the end.
- **VCF instant attack** — VCF envelope `*Attk 0` is a true instant attack, 1 is the stock attack.
- **Disk verify-after-write** — every floppy write is read back and checked; a bad write is retried.
- **Mistake prevention** — a second confirmation before destructive disk operations
- **Segment-pool exhaustion fix** (S950) — when the playback segment pool runs out, the chain is closed properly instead of playing past the sample's memory.
- **Faster disk loads** (S900) — a program is read a whole cylinder at a time instead of one block.

---

## Stock features adjusted or removed vs stock OS (S900 4.0 / S950 1.2b):

- **Service hex monitor** — removed, OS fault will show an error message - pressing any key will attempt to return to the menu instead of freezing. Stock would open service hex monitor (0xDEADC0DE)
- **RS232 serial** — removed, MIDI control is untouched
- **AUTO on `EDIT SAMPLE *06` was renamed to GATE** — cosmetic, for consistency with newly added Quiet-tail trim and to avoid confusion what `*AUTO` does next to `*0snap`
- **Time Direction moved to  `EDIT SAMPLE *04`** —  to make room for Slices page on `*05`
- **The MIDI-channel and OUTPUT-voices pages merged** `EDIT PRGM *16 (S950) \ *15 (S900)`.
- **OMNI set to OFF by default** — you're most welcome! 
- **ME35T (S950) and ASK90 (S900)** — removed alongside its dedicated pages (`UTILITY`, `DISK`). Use stock OS if you need this functionality. The UTILITY key opens the Madlabz OS info page now
- **Memory-full messages unified.** The various "out of space / full" messages are collapsed into one: `Full. Erase something`.
- **Compressed sample support (S900)** — Dropped OS 4.0 only feature. None of the standard disk libraries use sample compression. Other stock S900/S950 firmware doesn't support sample compression either. 
- **Velocity crossfade (VX) (S900)** — Another OS 4.0 only feature removed
- **Ensembles (disk sub-directories)  (S900)** — Reverted to pre-OS 4.0 single directory per disk
- **HD volume select over Program Change (S950 SCSI)** — moved onto `MIDI *04 *HD vol select`; its
  dedicated page is removed. The function is unchanged.
- **Digital input recording (S950)** — removed. Modern IB-109 clones skip the digital inputs entirely.

---

## Max objects / segments / available sample RAM

S900 has 512 Kwords, while S950 can be expanded up to 1536 Kwords of total RAM. Since OS needs to occupy low memory region. That leaves 464 Kwords for Sample Ram (S900) and 464 Kwords - 1488 Kwords for S950.  "Objects" is the shared directory for samples, programs and keygroups. "Segments" is a separate internal pool used to play long and looping samples.

Madlabz OS S900 version increases significantly objects and the sample count. The maximum samples you can have is 96 (useful when when working with Slices). Since each keygroup added deducts 1 object from the pool the effective limits are 64 samples if 2 samples (loud & soft) per keygroup or 48 samples when 1 sample per keygroup. Madlabz S950 variants maintain the status quo with S950 stock OS.

|                           | Stock S900 | **Madlabz S900** | Stock S950        | **Madlabz S950**  |
| ------------------------- | ---------- | ---------------- | ----------------- | ----------------- |
| Sample RAM                | 464 Kwords | 464 Kwords       | 464 - 1488 Kwords | 464 - 1488 Kwords |
| Max samples               | 32         | **48/64/96**     | 99                | 99                |
| Max objects               | 66         | **98**           | 198               | 198              |
| Max keygroups per program | 65         | **72**           | n/a               | n/a               |
| Max segments              | 200        | 200              | 300               | 300               |

---

## FAQ

In case you have questions:
- **Can you add another feature?** — I wish I could but I'm already hiting the limit on how much I can add in limited space.
- **Why the stock features were adjusted/removed?** — MADLABZ OS has to fit in exactly the same space as the stock firmware. The key objective was to retain the same sample RAM region as stock, so compromises had to be made.

---

## Documentation

- [User manual](docs/MADLABZ-OS.md) — every Madlabz OS feature explained
- [Changelog](CHANGELOG.md) — version history
