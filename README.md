# Ducker

MIDI-triggered sidechain ducker for [Schwung](https://github.com/charlesvestal/schwung) on Ableton Move. Classic pumping without an audio sidechain: a MIDI note, usually the kick, ducks the signal and lets it come back. The duck can work on the volume, on the volume turned upside down, or as a filter sweep.

## Setup

1. Load **Ducker** as an audio FX on the slot you want to pump (pads, bass, a whole mix).
2. Send the trigger note from another track, for example the drum track that plays the kick. Set that track's MIDI Out to a channel (Shift + Track).
3. On Ducker, set **Channel** to that channel (or **Omni**) and **Trigger** to the kick's note. Default: channel 1, note C1 (MIDI 36).

Only that one note on that channel triggers the duck. Every other note is ignored.

## Controls

| Page | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---|---|---|---|---|---|---|---|---|
| **Main** | Channel | Trigger | Mode | Depth | Attack | Hold | Release | Target |
| **Volume** | Curve | Vel Sens | Retrig | | | | | |
| **Filter** | Curve | Vel Sens | Retrig | | Cut1 | Cut2 | Res | Type |

The second page depends on Target: **Volume** for Volume and Volume Inv, **Filter** for Filter.

| Control | Range | Default | What it does |
|---|---|---|---|
| Channel | Omni, 1-16 | 1 | MIDI channel the trigger note comes in on |
| Trigger | C-2 to G8 | C1 | The note that triggers the duck |
| Mode | Trigger, Gate | Trigger | See [Mode](#mode) |
| Depth | 0-100% | 100% | How far the duck goes. 100% = silent (Volume) or all the way to Cut2 (Filter) |
| Attack | 0-50 ms | 5 ms | Time to duck down |
| Hold | 0-500 ms | 100 ms | Time spent fully ducked (Trigger mode only) |
| Release | 0-1000 ms | 300 ms | Time to come back up |
| Target | Volume, Volume Inv, Filter | Volume | See [Targets](#targets) |
| Curve | Linear, Expo, S-Curve, Pump | Linear | Shape of the duck, see [Curves](#curves) |
| Vel Sens | 0-100% | 0% | How much the trigger's velocity scales Depth |
| Retrig | Hard, Soft | Soft | What a new hit does while a duck is still running |
| Cut1 | 20 Hz-20 kHz | ~9.9 kHz | Filter cutoff while idle |
| Cut2 | 20 Hz-20 kHz | ~41 Hz | Filter cutoff at full duck |
| Res | 0-90% | 20% | Filter resonance |
| Type | LP12, LP24, HP12, HP24 | LP24 | Low-pass or high-pass, 12 or 24 dB per octave |

## Targets

- **Volume**: the classic duck. The signal plays at full volume and dips on every trigger.
- **Volume Inv**: the other way round. While the transport plays, the signal rests at 1 - Depth (silent at Depth 100%) and each trigger opens it up, to full volume at velocity 127. Use it for gated or "reverse pump" sounds that only speak on the kick. While the transport is stopped, the signal passes at full volume, so a stopped Set is never silent. The change between the two glides over about 10 ms, so starting or stopping makes no click.
- **Filter**: the trigger sweeps a filter from Cut1 to Cut2 and back. The sweep runs in octaves, so half the Depth lands halfway between the two cutoffs in pitch. At less than 100% Depth the sweep stops short of Cut2.
  - Low-pass (the default): Cut1 high, Cut2 low. The sound gets dark on the kick and opens up again.
  - High-pass: set Cut1 low and Cut2 high. The low end drops out on the kick.
  - The cutoff knobs have 128 steps on a log scale. Hold or turn one to see its frequency in Hz.

## Mode

- **Trigger**: each note-on plays the full envelope: Attack, Hold, Release. Note length does not matter.
- **Gate**: the duck holds as long as the note is held and releases on note-off. Hold is not used. A note shorter than the attack (a typical drum hit) still finishes the attack first, so every hit ducks the same amount.

## Curves

The curve shapes both the way down (attack) and the way back up (release).

- **Linear**: a straight line.
- **Expo**: starts slow and speeds up. On release the sound stays down longer, then comes back quickly.
- **S-Curve**: slow at both ends, fast in the middle. The smoothest of the four.
- **Pump**: straight attack, release that comes back fast and then levels off. The classic pumping sound.

## Velocity

**Vel Sens** sets how much the trigger's velocity matters. At 0% every hit ducks by the full Depth. At 100% a hit at velocity 127 gets the full Depth and a soft hit gets almost none. Values between mix the two. Harder hits always duck deeper, never less.

## Retrigger

What happens when a new hit arrives while the last duck is still running:

- **Hard**: the duck restarts from fully open, so a fast run of hits can click or flutter.
- **Soft** (default): the duck carries on from where it is. The attack keeps its slope, so it always reaches full duck at the same time after the hit. A hit that asks for less depth than is already ducked keeps the deeper level.

## Display

Attack, Hold and Release share one graphic:

- The envelope shows the duck over one beat (a quarter note) at the current tempo, so the right edge is where the next kick on the beat lands. Notches along the bottom mark 16th notes. Ducks longer than a beat run off the edge.
- The meter at the right shows the level right now. For Volume and Filter, a full bar means no ducking, and the lowest it goes is the hold level. For Volume Inv it is the other way up: low at rest, full on each trigger.

## Build and install

```bash
./scripts/build.sh     # Docker build, writes dist/ducker-module.tar.gz
./scripts/install.sh   # copies dist/ducker to move.local over ssh
```

Or install from the Schwung Module Store.

## AI Assistance Disclaimer

This module is part of Move Everything and was developed with AI assistance, including Claude, Codex, and other AI assistants.

All architecture, implementation, and release decisions are reviewed by human maintainers.  
AI-assisted content may still contain errors, so please validate functionality, security, and license compatibility before production use.
