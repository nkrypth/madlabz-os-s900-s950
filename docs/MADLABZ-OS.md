# MADLABZ OS 5.0 beta 4 for the AKAI S900 / S950

A new operating system for the AKAI **S900** and **S950** that adds a ton of new features. Boots from a floppy disk/image and loads into resident ram so you can easily return to your stock OS by just removing the Madlabz OS floppy disk/image and power cycling the sampler. 

---
# CONTENTS

- [1. Time stretch `EDIT SAMPLE *14`](#1-time-stretch-edit-sample-14)
- [2. Virtual Sample slicing `EDIT SAMPLE *05`](#2-virtual-sample-slicing-edit-sample-05)
- [3. Filter tracking and bypass (per keygroup)](#3-filter-tracking-and-bypass-per-keygroup)
- [4. FAT12 / WAV support (Experimental)](#4-fat12--wav-support-experimental)
- [5. Larger sample and keygroup limits (S900)](#5-larger-sample-and-keygroup-limits-s900)
- [6. Tempo-sync LFO](#6-tempo-sync-lfo)
- [7. Filter-cutoff LFO](#7-filter-cutoff-lfo)
- [8. The CC MAP — controlling the sampler over MIDI](#8-the-cc-map--controlling-the-sampler-over-midi)
- [9. DATA Encoder MIDI send — recording your edits into a DAW](#9-data-encoder-midi-send--recording-your-edits-into-a-daw)
- [10. Sample start, end and pitch over MIDI](#10-sample-start-end-and-pitch-over-midi)
- [11. Live filter cutoff over MIDI](#11-live-filter-cutoff-over-midi)
- [12. MIDI learn — keygroups and key ranges by playing](#12-midi-learn--keygroups-and-key-ranges-by-playing)
- [13. Portamento / glide](#13-portamento--glide)
- [14. Zero-crossing snap](#14-zero-crossing-snap)
- [15. Quiet-tail trim (`*Gate`)](#15-quiet-tail-trim-gate)
- [16. VCF instant attack](#16-vcf-instant-attack)
- [17. Segment-pool exhaustion fix (S950)](#17-segment-pool-exhaustion-fix-s950)
- [18. Disk reliability — verify after write](#18-disk-reliability--verify-after-write)
- [19. Mistake prevention](#19-mistake-prevention)

---
# MADLABZ OS features explained

## 1. Time stretch `EDIT SAMPLE *14`

Native to S950 now also available for S900 users.

```
*14 STRETCH *New name NEW SAMPLE  *200%
 *D-time 1000 *Auto D_ *Mon1/pol2 2 *Do_
```

| Field       | Effect                                                                                                                                           |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `*New name` | The name for the stretched result. It **must differ** from the source name — stretch always makes a new sample. The placeholder is `NEW SAMPLE`. |
| `*200%`     | The new length as a percentage of the original. `200%` is twice as long (half the tempo); below 100% is shorter. The pitch does not change.      |
| `*Do`       | ENT runs the stretch.                                                                                                                            |

The remaining fields (`*D-time`, `*Auto D`, `*Mon1/pol2`) tune the stretch quality — the processing
window and whether it is chosen automatically. Start with `*Auto D` and adjust to your taste.

**Note:** If you leave `*New name` the same as the source, the stretch is refused with `NO! Must be new
name` — rename the output and run `*Do` again.

## 2. Virtual Sample slicing `EDIT SAMPLE *05`

Slicing carves a loaded sample into independently playable **slices** — handy for chopping a drum
loop into its individual hits, or a phrase into words. A slice is just a small object entry that
plays a window over its parent sample's audio: slices **share the parent's audio and use no extra
Sample RAM**, so N slices cost N objects from the pool and nothing more.

```
*05  * 2   *Slice  *Add Slice  *Crs*Fin
    SLICES   # 1     (ENT)_      00,000
```

| Field           | Range                      | Effect                                                                                                         |
| --------------- | -------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `SLICES`        | 0 to 64                    | The even-division grid size that will split sample to N slices. To return to full sample length select 0       |
| `*Slice`        | 1 to N                     | Slice index - which slice you currently have selected                                                          |
| `*Crs` / `*Fin` | `cc,fff` (0,000 to 16,383) | The slice's start point over the parent, as a 14-bit normalized value (the entire length of the parent sample) |
| `*Add Slice`    | ENT                        | Creates (or replaces) the slice at the current index.                                                          |

Each slice is named from the first **five characters of the parent name**, then `#SL`, then the
two-digit index — example: `LOOPTONE` gives slices `LOOPT#SL01`, `LOOPT#SL02`, and so on. Because the name is also the key, pressing `*Add Slice` again on the same index **replaces** that slice rather than making a duplicate.

Once created, a slice behaves like any sample: select it into a program keygroup and it plays its region of the parent, with the parent's replay mode and loop. You can further modify the sample start/end points and even loop them.

**Audition before you commit.** To hear the current slice press the PB key

**Render a slice into a real sample.** A slice depends on its parent staying in memory. To turn a slice into a standalone sample —  select the slice as current sample, go to S950 `EDIT SAMPLE *10` \ S900 `*09`, and press ENT on the "discard before start, after end" action. It bakes the slice's window into its own audio, in the same slot under the same name.

### Example — slicing a drum loop into eight slices

1. Load the drum loop and select it in EDIT SAMPLE.
2. Go to `EDIT SAMPLE *05`. Set `SLICES` to `8`.
3. Set `*Slice` to `# 1` (the start of the loop). Press PB to audition. If the first hit isn't exactly on the grid, nudge `*Crs`/`*Fin`.
4. Press ENT on `*Add Slice`. Slice `LOOPT#SL01` is created.
5. Set `*Slice` to `# 2`. Audition, nudge if needed, then `*Add Slice`.
6. Rinse and repeat. You now have eight slices, each playable from its own key when assigned to keygroups, all sharing the loop's audio.

## 3. Filter tracking and bypass (per keygroup)

Use `*FltrTrack` to adjust filter tracking or completely bypass anti-alias filter. It is set **per keygroup** on `EDIT PROGRAM *04`. Each keygroup can carry its own mode.

```
*04   *FltrTrack  *VelSw
 *KG1  0=DEFAULT    128
```

| `*FltrTrack` | Shows       | Effect on the sound                                                                        |
| ------------ | ----------- | ------------------------------------------------------------------------------------------ |
| 0            | `0=DEFAULT` | Same as stock OS                                                                           |
| 1            | `1=+1 OCT`  | The same tracking, one octave higher.                                                      |
| 2            | `2=+2 OCT`  | Two octaves higher                                                                         |
| 3            | `3=NoTrack` | Only the program's own filter, key-follow, velocity and filter envelope decide the cutoff. |
| 4            | `4=Open16k` | The filter forced wide open at 16 kHz                                                      |
| 5            | `5=Open20k` | The filter forced wide open at 20 kHz                                                      |

Because the mode belongs to the keygroup, it is **saved with the program** (SAVE PROGRAM).  You can also switch the mode live over MIDI (CC 21 — see the CC MAP).

## 4. FAT12 / WAV support (Experimental)

S950 FAT and S900 FAT builds can read both Akai and PC-formatted FAT12 floppies. WAV are import only. All  mono PCM, 8 or 16 bit, up to 48 kHz should be loading fine. 

**File names.** The sampler's 10-character names become DOS 8.3 names. A space, `+` or `.` in a name
is written as `_`, and an `_` in a file name is shown as a space on the sampler.

**Formatting a FAT floppy.** 

```
*08  DISK FORMAT *Type 0 (0=Akai, 1=FAT)
    *Prepare new disk (ENT) _       xxx%
```

The format page has a `*Type` field. Set `*Type` to `1` and press ENT on `*Prepare new disk` 

**Saving.** WAV is **import only**. Saving to a FAT floppy writes one native Akai file per object:
samples as `.S9`, programs as `.P9` (a program file carries its keygroups inside it), in the same
`akaiutil` convention, so they reload on either machine and can be read by `akaiutil` on a PC.

**Loading.** Load from a FAT floppy with the usual DISK load pages. The files the sampler lists are:

- **`.WAV` files** must be **mono PCM, 8 or 16 bit, up to 48 kHz** else it will be refused with `OOPS! WAV: mono PCM 8/16-bit <=48kHz`. An 8-bit WAV is converted to 12 bit on load. A WAV that carries an Akai `S9H ` header chunk gets its sampler settings (loop, tuning and so on) back on load; any other WAV loads as a one-shot.
- **Akai objects** stored as files, named `NAME.<type>9` in the convention the `akaiutil`  uses: `.S9` sample, `.P9` program, and so on.

Other files on the disk are ignored.

## 5. Larger sample and keygroup limits (S900)

This build holds **98 objects**: with one program and one keygroup loaded that is up to 96 samples. See section D.
It has also S950-style menu that allows loading more than one disk. `DISK *01` has a three-way load menu:

```
*01 *Clear mem & load disk (1), Mem-xxx%
Load disk (2), Clear prev (3) _ Dsk-xxx%
```

| Key | Action                                                                                                                                        |
| --- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `1` | Clear memory, then load the whole disk.                                                                                                       |
| `2` | Load the whole disk **on top of** what is already in memory, without clearing it — swap in a second floppy and press `2` to add its contents. |
| `3` | Clear the samples and programs from memory without loading anything (one program stays as the current program).                               |

`1` and `3` clear memory, so they ask for a second ENT. `2` clears nothing and loads straight away. If
the directory fills up partway through a load, the sampler stops with its memory-full message and keeps
everything loaded so far.

## 6. Tempo-sync LFO

While MIDI clock is running, the keygroup LFO stops free-running and **locks to the clock**: the
keygroup's own LFO Rate value selects a musical beat division, so every voice's LFO cycles in time
with the song. When the clock stops, the LFO goes back to free-running after a short moment.

There is no new field to set. The stock keygroup LFO Rate value (0–99) doubles as the division
selector. It is the `*Rate` field on `EDIT PROGRAM` **LFO** (S950 `*13` shown; S900 `*12`, the same screen):

```
*13   LFO.  *Depth *Rate *Delay *Desync.
 *KG1          0     42    64      ON
```

| LFO Rate | Division         |
| -------- | ---------------- |
| 0 to 11  | 1/1 (whole note) |
| 12 to 23 | 1/2 (half)       |
| 24 to 35 | 1/4 (quarter)    |
| 36 to 47 | 1/4 triplet      |
| 48 to 59 | 1/8 (eighth)     |
| 60 to 71 | 1/8 triplet      |
| 72 to 83 | 1/16 (sixteenth) |
| 84 to 99 | 1/16 triplet     |

The lock is **global** — it applies to every sounding voice while the clock runs — and each keygroup
cycles at the division its own LFO Rate names. This keeps tempo-locked filter and pitch wobbles
(vibrato, tremolo and filter sweeps) in time with the song.

**LFO Rate 0 does not stop the LFO** — Rate is a speed, not an on/off switch. With the clock running,
Rate 0 selects the slowest division (1/1) and the LFO still cycles; free-running, Rate 0 is the slowest
free-run speed, not a halt. So a note can still show pitch or filter movement at Rate 0; this is
expected. To silence one destination, set *that* destination's LFO depth to zero — the pitch LFO depth
for vibrato, the filter-cutoff LFO depth (`*Cutoff`, where 0 is off) for the filter sweep — which leaves
the other destination untouched.

## 7. Filter-cutoff LFO

A second place the keygroup LFO can go: the **filter cutoff**. The same LFO that gives vibrato can pump
the filter in time — the tempo-synced "filter pump" of french house and related styles. Pitch and
filter each have their own depth, so the one LFO drives them independently.

Set it on `EDIT PROGRAM` **LFO DEPTH** (S950 `*14` shown; S900 `*13`, the same screen), the `*Cutoff` field:

```
*14 LFO DEPTH  *Aft.tch *Mod.whl *Cutoff
 *KG1               0       50     +00
```

| `*Cutoff`     | Effect                                                              |
| ------------- | ------------------------------------------------------------------- |
| `0`           | Off — the filter is left exactly as the cutoff and envelope set it. |
| `+1` to `+50` | The LFO opens the filter; at `+50` it sweeps the full range.        |
| `-1` to `-50` | The LFO closes the filter; at `-50` it sweeps the full range.       |

The depth is **signed**: the sign is the direction the sweep leans, the number is how far it reaches.
From a bright cutoff a negative depth pumps down; from a dark cutoff a positive depth pumps up (the far
end simply sits against the limit).

It uses the one keygroup LFO, so it rides on the **LFO Rate**, **LFO Delay** fade-in and **tempo-sync**
already set for that keygroup — there is no second rate to dial, and under MIDI clock the pump locks to
the beat just like the pitch LFO. LFO Rate sets the speed; as with every LFO here, **Rate 0 is the
slowest setting, not off** — to switch the filter LFO off, set `*Cutoff` to `0`.

It also works with **no filter envelope**: set `VCF Amount` to `0` and the LFO still pumps the plain
cutoff, for a pure filter sweep with nothing else moving.

Over MIDI it is **CC 25**, centre-detented — `64` is off, below negative, above positive, clamped to
`-50..+50` (the same centre-64 feel as VCF Amount). The value is stored in the program and kept by
**SAVE PROGRAM**.

## 8. The CC MAP — controlling the sampler over MIDI

Madlabz OS routes incoming MIDI control changes to the current program's synthesis parameters, so any
knob or fader on your controller can drive the sampler. 

The map covers **33 parameters** in all: 29 keygroup synthesis parameters, plus four that act on the
note or play window (Pitch, Sample Start, Sample End, and the filter tracking mode).
The **same map is used on both the S900 and the S950** — there is no separate S900 CC map. Two of the
34 — Velocity-crossfade mix (CC 119) and Velocity crossfade on/off (CC 86) — have **no audible effect
on either build**: the S950 has no velocity crossfade, and the S900 build leaves it out (see section C). They remain in the map as inert entries.

The sampler receives these CCs under the usual MIDI reception rule: on its **Basic channel only** when
OMNI is off (the power-up default), or on **every channel** when OMNI is on. The send side — panel
edits transmitting their CC into a DAW — is a separate feature (see **Knob MIDI send**).

**A received CC edits the program, exactly like turning the panel knob.** This is important: a mapped
CC **overwrites the program's stored value** across all keygroups of the current program, the same way
the panel knob does with `*KGall` selected. The change is kept by **SAVE PROGRAM**. It is *not* a
temporary "performance only" overlay. The one exception is **Pitch** (CC 22), a live transpose that is
never written into the program (see below).

Because it overwrites the stored value, sending CC value `0` writes `0` into that parameter — it does
not "restore" the old value. To put a parameter back, send the value you want.

### The fixed CC map

The table below is the full built-in map. "Range" is the value the parameter takes and how the
received CC value maps onto it. "Controls" names what each CC drives, in plain terms.

Any values above the maximum range will be clamped to maximum value e.g. 127 -> 99.
For bipolar ranges 64 = 0 (center point), below negative  e.g. 14 → −50, above positive e.g. 114 → +50
For ON/OFF switches 0–63 = OFF, 64–127 = ON

| Parameter         | CC  | Range                          | Controls                                                                       |
| ----------------- | --- | ------------------------------ | ------------------------------------------------------------------------------ |
| AMP Env Attack    | 73  | 0–99                           | Amplitude envelope attack                                                      |
| AMP Env Decay     | 75  | 0–99                           | Amplitude envelope decay                                                       |
| AMP Env Release   | 72  | 0–99                           | Amplitude envelope release                                                     |
| AMP Env Sustain   | 79  | 0–99                           | Amplitude envelope sustain                                                     |
| Filter Cutoff     | 74  | 0–99                           | Filter cutoff                                                                  |
| VCF Amount        | 70  | −50…+50                        | Filter-envelope amount                                                         |
| LFO Rate          | 76  | 0–99                           | LFO rate                                                                       |
| LFO Depth         | 77  | 0–99                           | LFO depth                                                                      |
| LFO Delay         | 78  | 0–99                           | LFO delay                                                                      |
| Filter-cutoff LFO | 25  | −50…+50                        | Filter-cutoff LFO depth                                                        |
| Porta             | 5   | 0–127                          | Portamento / glide time                                                        |
| VCF Env Attack    | 102 | 0–99                           | Filter-envelope attack                                                         |
| VCF Env Decay     | 103 | 0–99                           | Filter-envelope decay                                                          |
| VCF Env Sustain   | 104 | 0–99                           | Filter-envelope sustain                                                        |
| VCF Env Release   | 105 | 0–99                           | Filter-envelope release                                                        |
| Filter KeyTrack   | 106 | 0–99                           | Key-to-filter tracking                                                         |
| LFO Aftertouch    | 107 | 0–99                           | LFO depth from aftertouch                                                      |
| LFO Mod Wheel     | 108 | 0–99                           | LFO depth from mod wheel                                                       |
| Vel > Filter      | 109 | 0–99                           | Velocity to filter                                                             |
| Vel > Attack      | 110 | 0–99                           | Velocity to attack                                                             |
| Vel > Release     | 111 | −50…+50                        | Velocity to release                                                            |
| Vel > Loudness    | 112 | 0–99                           | Velocity to loudness                                                           |
| Loudness          | 113 | −50…+50                        | Keygroup loudness, both sample layers                                          |
| Warp Velocity     | 115 | 0–99                           | Warp velocity                                                                  |
| Warp Atk Offset   | 116 | −50…+50                        | Warp attack offset                                                             |
| Const Pitch       | 85  | OFF / ON                       | Constant pitch on/off                                                          |
| One-shot          | 117 | OFF / ON                       | One-shot on/off                                                                |
| LFO Desync        | 118 | OFF / ON                       | LFO desync on/off                                                              |
| Filter Mode       | 21  | modes 0–5                      | Filter tracking/bypass mode (`*FltrTrack`) (0–15 = 0, 16–31 = 1, … 80–127 = 5) |
| Pitch             | 22  | −16 … +15¾ semitones           | Wide transpose, live per channel in ¼ steps (CC 64 = no change)                |
| Sample Start      | 23  | 0–16383, 14-bit / 0-127, 7-bit | Live play-window start                                                         |
| Sample End        | 24  | 0–16383, 14-bit / 0-127, 7-bit | Live play-window end                                                           |

A received value is always clamped to the parameter's own range, so a controller can never push a
parameter past a value you could type on the panel. CC value `0` always writes `0` (on a ±50
parameter that is the centre); it does not restore an old value.

**Which rows reach a note already sounding.** Filter Cutoff (CC 74) and Pitch (CC 22) move notes that
are already playing. The others take effect from the next note on.

### CCs the OS keeps for itself

A few control changes are handled by the OS before the map ever sees them, and keep their standard
MIDI meaning: **mod wheel (CC 1)**, **volume (CC 7)** and **sustain (CC 64)**. They are not part of the
fixed map, so there is nothing to conflict with.

## 9. DATA Encoder MIDI send — recording your edits into a DAW

This is the **send** side only. Editing a mapped keygroup parameter from the front panel **transmits**
its control change on the Basic MIDI channel, so a DAW can record your edits. It does **not** receive:
*receiving* CC to control the sampler is the separate **CC MAP** (above). *Sending* your panel edits is
this feature. (A DAW can of course play your recorded edits back into the unit, where the CC MAP then
applies them.) Turn the DATA wheel or type a value on a mapped `EDIT PROGRAM` keygroup field and the
unit sends that parameter's CC each time the value changes — one message per step.

The **25 wide-range keygroup parameters transmit** — the direct and signed rows in the map (CC 73, 75,
72, 79, 74, 70, 76, 77, 78, 5, 102, 103, 104, 105, 106, 107, 108, 109, 110, 111, 112, 113, 115, 116,
119). A direct parameter sends its value as is; a signed parameter sends 64 plus the value (so a stored
`−1` sends 63, `+5` sends 69). Transmit always uses the Basic channel, whatever the OMNI setting.

The **switch rows and the special rows do not transmit** on a panel edit — Const Pitch, Vel X-fade On,
One-shot, LFO Desync, Filter Mode, Pitch, Sample Start and Sample End.

## 10. Sample start, end and pitch over MIDI

Three mapped CCs act on the note or the play window rather than on an ordinary synthesis parameter.

### The Sample Start / End window

Sample Start (CC 23) and Sample End (CC 24) together define a **play window** — the stretch of the
sample a note plays — and they work across the **whole sample**, so a note can start deep inside a
large sample. This turns one sample into a live break-chopper: sweep Sample Start from a sequencer and
the sample restarts from a new place on each note, and move Sample End to change how much plays.

- **Sample End sets where the note stops**, as a point across the whole sample. `0` means the sample's
  own end.
- **Sample Start sets where the note begins**, as a point between the start of the sample and the
  current End, so Start always stays before End. `0` means the sample's own start.
- **The window is live, not saved.** It applies to every keygroup of the current program from the next
  note on, and loading a program clears it.

**It is 14-bit, and plain 7-bit gear still works.** Fine control uses a 14-bit value made of a coarse
(MSB) CC plus an optional fine (LSB) CC. The fine CC number is the coarse number plus 32 — so with
Sample Start on CC 23 and Sample End on CC 24, the fine CCs are 55 and 56. A controller that only sends
a single 7-bit CC still works: that one CC gives coarse control over the full sample; the optional fine
CC refines it.

### Pitch over MIDI

**Pitch** (CC 22) is a wide transpose, independent of the pitch wheel, in quarter-semitone steps. Use
it to sweep whole phrases up or down from a sequencer, or for microtonal detuning:

| CC value | Pitch                                  |
| -------- | -------------------------------------- |
| 0        | −16 semitones                          |
| 63       | −¼ semitone                            |
| 64       | no change (also the state at power-up) |
| 65       | +¼ semitone                            |
| 68       | +1 semitone                            |
| 127      | +15¾ semitones                         |

**Notes.** Notes already sounding retune immediately. It is independent of the pitch wheel and adds on
top of it. Value `0` is **−16 semitones, not "off"**, so send `64` to return to normal pitch. Pitch is
the one mapped CC that is **not written into the program and not saved** — it is a live transpose,
tracked separately per MIDI channel.

## 11. Live filter cutoff over MIDI

Filter Cutoff is CC 74 in the map, and it reaches notes **already sounding** as well as the next ones —
sweep it from a controller and the filter opens and closes on held notes in real time. It moves both
sample layers of the keygroup together. Like other mapped CCs it also writes the program's stored
cutoff, so a value you leave in place is kept by SAVE PROGRAM.

## 12. MIDI learn — keygroups and key ranges by playing

Two `EDIT PROGRAM` edits can be made by playing a note instead of dialling a number — a panel note or a
MIDI note, either works. This is the fast way to lay out a multisample. In both cases the played note
still sounds, so you always get confirmation.

**Pick a keygroup by ear.** Hold the **EDIT PROG** key and play a note: the `*03 KEYGROUP *Select`
field jumps to the keygroup that note plays. When several keygroups overlap on one key, the
highest-numbered one covering it is selected.

**Set a key range by playing.** Put the cursor on a `*Low key` or `*Hi key` field — on the soft-sample
page `EDIT PROGRAM *05` or the loud-sample page `*07` — and play a note: the field, and the keygroup's
range, take that note. Play the low key, then the high key. A note played to set a *low* key is
normally below the keygroup's current range; it is captured and sounded anyway, so you hear what you
set instead of silence.

This is note-to-value capture only; the CC map is fixed and is not affected.

## 13. Portamento / glide

With a portamento time set, this build plays a real note-to-note **pitch glide**: each new note slides
from the previous note's pitch to its own. It is a dedicated per-voice glide, set by `*Porta` on
`EDIT PROGRAM *12` (S900 `*11`):

```
*12   WARP    *Velocity  0    *Time   99
 *KG1   *Attack offset +00    *Porta   0
```

S900:

```
*11   WARP    *Velocity  0    *Time   99
 *KG1   *Attack offset +00    *Porta   0
```

| Field | Range | Default | Effect |
|---|---|---|---|
| `*Porta` | 0 to 127 | 0 | The glide time. `0` is off; higher values glide more slowly. |

Set it per keygroup (or to every keygroup with `*03` on `*KGall`), or drive it live over MIDI with
CC 5. This is a separate glide from the stock `*Time` Warp-Time control on the same page, which the
panel keeps alongside it.

**Notes.** Each new note slides from the previous note's pitch, and bigger pitch jumps take
proportionally longer. The first note of a phrase has nothing to glide from, so it plays straight.
Changing `*Porta` changes the glide speed live.

## 14. Zero-crossing snap

A `*0Snap` action on the START, END and LOOP point pages (`EDIT SAMPLE *06` / `*07` / `*08`) snaps the
point you are editing to the nearest **zero crossing** — the place where the waveform passes through
silence — for click-free starts and ends and glitch-free loops. The LOOP page (S950 shown; the S900
page differs only in the spacing of the numbers):

```
*08  LOOP LENGTH  *Crs *Fin *AUTO *0Snap
     (5 -     1,800)    0,045 ENT (ENT)
```

**How to use it.** Set the point roughly with `*Crs` (coarse) and `*Fin` (fine), then press ENT on
`*0Snap`.

**Notes.** The snap searches up to **64 samples either side** and moves the point to the crossing nearest where you set it. If there is no zero crossing within range, the point is left exactly where you put it.

## 15. Quiet-tail trim (`*Gate`)

A `*Gate` field uses a loudness threshold to trim silence off a sample. The value (0–99) is the
threshold: a higher number treats louder material as "quiet", trimming more.

```
*06  START POINT  *Crs *Fin *Gate *0Snap
     (0 -     1,700)    0,000 01  (ENT)_
```

```
*07  END POINT    *Crs *Fin *Gate *0Snap
(0000,100-    1,800)    1,800 01  (ENT)_
```

(S950 shown; the S900 pages differ only in the spacing of the numbers.)

- **`EDIT SAMPLE *07 *Gate`** trims **trailing** silence: it pulls the END point back past the quiet
  tail. This is the Madlabz addition. It works across the **whole sample** (any length), not just one
  part of it.
- **`EDIT SAMPLE *06 *Gate`** trims **leading** silence: it advances the START point past quiet at the
  beginning. This is the stock automatic start-search, shown here as `*Gate` (its stock label was
  `*AUTO`). The LOOP page `*08` keeps its `*AUTO` label.

**How to use it.** Set the threshold value and the trim applies. Use it to clean the tails of recorded
or time-stretched samples.

**Note.** Both `*Gate` fields default to `01`, and merely walking the cursor past the field will **not**
silently re-trim a point you set by hand — only an actual change to the value re-trims.

## 16. VCF instant attack

On the VCF (filter) envelope, an attack of **`*Attk 0` is a true instant attack** — the filter envelope
jumps straight to full and begins its decay in the first moment. `*Attk 1` and above behave exactly
like the stock attack, so `0` is the one new setting below the stock minimum, giving a snappy filter
transient the stock OS could not reach.

The field is `EDIT PROGRAM *09 *Attk` (S900 `*16 *Attk`). It only changes the sound when the VCF
envelope is actually doing something — that is, when the VCF amount (`*Amnt` on the same page) is not
zero.

```
*09 VCF *Attk *Dcay *Sstain *Rlse *Amnt
 *KG1     20    20     20     20   +00
```

S900:

```
*16 VCF *Attk *Dcay *Sstain *Rlse *Amnt
 *KG1     20    20     20     20   +00
```

## 17. Segment-pool exhaustion fix (S950)

When sample's playback chain takes the **very last free segment** stock OS leaves the chain without an end marker, so the voice reads on past the sample into other memory which makes sample sound corrupted.

Madlabz OS closes the chain at the last segment it got, so a voice can never run past the sample. The operation that ran the pool out then stops with the `CRASH! YIKES!` recovery banner and returns to the menu after you press any key.

## 18. Disk reliability — verify after write

Every floppy sector written by the OS is immediately **read back and checked**, so a bad disk or a
mis-written sector is caught at save time instead of silently committed. A failed check rewrites the
sector automatically; only if that still fails does the OS report an error:

|      | Check                                                      | Automatic rewrites | Error shown                                |
| ---- | ---------------------------------------------------------- | ------------------ | ------------------------------------------ |
| S950 | the drive's CRC on the read-back                           | up to 2            | `OOPS! Bad disk data.`                     |
| S900 | the read-back compared byte for byte with what was written | 1                  | `OOPS! DISK WRITE ERROR: readback differs` |

In normal use you will never see this; when you do, it means the media is bad — use a different disk.

**Faster loads (S900).** When a program spans several blocks, the S900 build reads each run of blocks
on one cylinder with a single disk command instead of one command per block, so large programs load
faster. Samples load as before.

## 19. Mistake prevention

The operations that clear a disk or wipe memory ask you to `press ENT a second time` before they proceed:

- **S950:** SAVE ALL (`DISK *05`), the load-all-samples action (`DISK *04 *All samples`), and FORMAT /
  WIPE (`DISK *09`, floppy / hard disk / wipe; `DISK *08` FORMAT on the S950 FAT build).
- **S900:** `DISK *04` (clear disk and save entire memory), `DISK *08` FORMAT, and the two load-menu
  choices that clear memory on `DISK *01` (`1` and `3`).

The first ENT arms the action and shows:

```
Press ENT again
```

Pressing any other key cancels; a second ENT confirms.

**Graceful fault recovery.** If the OS hits an internal fault it shows a brief banner, silences the
voices, and **returns to the menu** rather than freezing. The banner is:

```
CRASH! YIKES!
```

Nothing dramatic happens — you are simply back at the main menu and can carry on. (The stock OS dropped
into a hex service monitor and stayed frozen until you power-cycled.)

---


