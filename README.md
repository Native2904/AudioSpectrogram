# AudioSpectrogram

**Lister plugin (WLX) for Total Commander** that shows music files as a spectrogram, together with technical data and measurements. It compares up to four files side by side and writes logs for whole folders.

The plugin **measures and displays. It does not rate.** Files are only read, never changed.


<img width="1920" height="988" alt="decaud-goldberg-aria-dsd64" src="https://github.com/user-attachments/assets/2a2769b9-d81a-4901-837d-3698188eb9a9" />


---

## Contents

- [Features](#features)
- [Installation](#installation)
- [Keys and mouse](#keys-and-mouse)
- [Views](#views)
- [Measurements](#measurements)
- [Measurement accuracy](#measurement-accuracy)
- [Formats](#formats)
- [DSD](#dsd)
- [Folder log](#folder-log)
- [Settings](#settings)
- [Detect string](#detect-string)
- [System requirements](#system-requirements)
- [Building from source](#building-from-source)
- [Version history](#version-history)
- [Licence and third-party code](#licence-and-third-party-code)

---

## Features

- **Spectrogram, average spectrum, third-octave bands, measurement table**
- **Zoom** into time and frequency; the visible part is analysed again in finer detail
- **Playback** with a self-drawn transport bar (play / pause, stop, loop, volume)
- **Comparison** of up to four files: stacked spectrograms, overlaid curves, one table column per file
- **Measurements**: cutoff frequency, bits used, sample and true peak, RMS (AES17), crest factor, loudness (EBU R128), loudness range, DR, clipping, DC offset, stereo balance and correlation
- **Folder log** as CSV table or readable text report with album DR
- **DSD** (DSF, DFF) with its own decoder; levels measured in the hearing band
- **Seven colour schemes**, light and dark mode, text size 100–200 %
- **Cache** for measurements, so files open instantly the second time
- Language file for the plugin texts (English and German included)

## Installation

Open the ZIP in Total Commander and confirm the installation.

When updating from a version before 1.5.0, add `| EXT="DSF" | EXT="DFF"` to the detect string in `wincmd.ini` (see [Detect string](#detect-string)); Total Commander keeps the old string.

Updating from 1.0.0: the plugin was renamed from *AudioSpectogram* to *AudioSpectrogram*. Total Commander treats it as a new plugin. Remove the old one under Configuration → Plugins → Lister and delete its folder. The old cache `%LOCALAPPDATA%\AudioSpectogram` can be deleted as well.

## Keys and mouse

| Key / action              | Function                                                                           |
| ------------------------- | ---------------------------------------------------------------------------------- |
| Click in the spectrogram  | Play from this position                                                            |
| Space                     | Pause / continue; starts playback if nothing plays                                 |
| Esc                       | Stop playback; Esc again closes Lister                                             |
| Numpad + / −, Up / Down   | Volume in steps of 5 % (while the playback bar is visible)                         |
| Page Down / Page Up       | Next / previous music file in the folder, by name, also when only one file is open |
| N / P                     | Next / previous file of the selection in Total Commander                           |
| Mouse wheel               | Zoom the time axis at the mouse position                                           |
| Ctrl + mouse wheel        | Zoom the frequency axis                                                            |
| Drag with the left button | Move the zoomed view                                                               |
| Double click              | Reset zoom                                                                         |
| Ctrl+C                    | Copy measurements                                                                  |
| Right click               | Menu: views, channels, compare, save image, folder log, settings                   |

## Views

- **Spectrogram**: X = time, Y = frequency, colour = level in dB. A dashed line marks the measured cutoff frequency.
- **Average spectrum**: level over frequency for the whole file.
- **Third-octave bands**: the average spectrum summed in 1/3-octave bands (IEC 61260). A full-scale sine reads 0 dB in its band.
- **Measurement table**: all values; in comparison one column per file, and "Mark differences" shows differing values in bold.

**Measuring cursor**: move the mouse over the chart; time, frequency and level appear at top right.

**Status line**: decoder, FFT size, Hz per bin, overlap of the FFT windows and time per image column. While zoomed, a larger FFT may be used; the status line shows the size in use.

**Playback bar**: below the time axis or above the chart. Position, size (100–200 %) and weight (light, semibold, bold) are set in the settings. Volume only gets quieter; 100 % is the original level. Only the sound changes, never a measurement.

**Colour scale**: the colour stands for the level, not for the frequency. Schemes: Standard, Aurora, Viridis, Magma, Fire, Green, Grey. In every scheme the brightness rises evenly with the level. Viridis is readable with red-green colour blindness, Grey is meant for printing.

**Comparison**: right click → Compare. Every file loaded afterwards with N or P is added (four at most). The axes follow the longest file and the highest sample rate. There is no ranking.

## Measurements

| Value                       | Definition                                                                                                                                                                           |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Bitrate (header / file)     | As stated in the file / file size divided by duration                                                                                                                                |
| Encoder (header / stream)   | Xing/Info header of an MP3 (with LAME lowpass) / signature inside the music frames                                                                                                   |
| Cutoff                      | Highest frequency where the mean level in the kHz below is at least 20 dB above everything above it. A second search finds the end of the content in upsampled material (see below). |
| Slope                       | Steepness of that drop in dB per kHz                                                                                                                                                 |
| Above 24 kHz                | Level of the band from 24 kHz to half the sample rate (sample rates above 48 kHz)                                                                                                    |
| Bits used                   | Bit depth minus low bits that are zero in all samples (WAV, FLAC)                                                                                                                    |
| Sample peak                 | Largest sample magnitude, dBFS                                                                                                                                                       |
| True peak                   | Peak after 4× oversampling (2× from 88.2 kHz), dBTP                                                                                                                                  |
| RMS                         | Root mean square of all channels, referenced as in AES17 / IEC 61606: full-scale sine = 0 dBFS                                                                                       |
| Crest factor                | Ratio of sample peak to RMS in dB; a sine has 3.01 dB                                                                                                                                |
| Loudness                    | Integrated loudness per ITU-R BS.1770 / EBU R128, LUFS                                                                                                                               |
| Loudness range              | EBU Tech 3342, LU                                                                                                                                                                    |
| DR                          | Re-implementation of the well-known DR method: 3-second blocks, second highest block peak against the RMS of the loudest 20 %; mean of the channels, rounded                         |
| Clipping                    | Places with at least 3 consecutive samples at ≥ 99.99 % of full scale                                                                                                                |
| DC offset                   | Largest DC component of any channel, percent of full scale                                                                                                                           |
| Balance, Correlation, L = R | Level left minus right, correlation (−1 to +1), identical channels (stereo only)                                                                                                     |

Spectrum basis: FFT 2048, Hann window. A full-scale sine on a bin frequency reads 0.0 dB.

**Cutoff of upsampled material**: a resampling filter lets the highs fade over 2–4 kHz instead of cutting at a cliff. If no edge is found, a second search looks for the end of the content: between 14 kHz and 80 % of half the sample rate, signal below from −135 dB, and at most −150 dB in the 3 kHz above a gap of 2 kHz. A real recording keeps its noise floor up to half the sample rate and gives no cutoff.

## Measurement accuracy

No conformance to a standard is claimed. Listed is what each value was checked against, the number of cases and the largest deviation observed.

| Value                | Checked against                           | Cases | Largest deviation              |
| -------------------- | ----------------------------------------- | -----:| ------------------------------ |
| DR per channel       | foobar2000 DR Meter 1.0.8                 | 50    | 0.00 dB                        |
| DR per file          | foobar2000 DR Meter 1.0.8                 | 41    | 0                              |
| Sample peak          | foobar2000 DR Meter 1.0.8                 | 41    | 0.00 dB                        |
| Sample peak          | Exact Audio Copy 1.8                      | 15    | < 0.1 %                        |
| RMS (AES17)          | foobar2000 DR Meter 1.0.8                 | 15    | 0.08 dB                        |
| Balance L/R          | foobar2000                                | 15    | 0.01 dB                        |
| Loudness             | EBU Tech 3341 test signals 1–5            | 5     | 0.02 LU                        |
| Loudness, 5.1        | EBU Tech 3341 test signal 6               | 1     | 0.02 LU                        |
| Loudness range       | EBU Tech 3342 test signals 1–4            | 4     | 0.00 LU                        |
| True peak            | sines with known true peak                | 5     | 0.17 dB low                    |
| K-weighting filter   | ITU-R BS.1770-4 coefficients              | 8     | 14 decimals equal              |
| Crest factor         | expected values of test files             | 26    | < 0.05 dB                      |
| Vorbis, Opus decoder | ffmpeg decoding the same file             | 2     | equal                          |
| DSD → PCM            | same filters in Python (numpy / scipy)    | 1     | 3·10⁻⁸                         |
| DSD → PCM            | ffmpeg 6.1 decoding the same file         | 1     | level 0.000 dB, rest −107 dB   |
| DSD levels           | PCM source and DSD64 made from it         | 1     | 0.01 dB (peak, LUFS, DR)       |
| DSD levels           | foobar2000 DR Meter 1.0.8 (DSD64/128/256) | 4     | peak and DR equal, RMS 0.09 dB |
| Third-octave bands   | exact band edges, sines and noise         | 5     | 0.01 dB (sines)                |

The EBU test signals were generated from the EBU descriptions; they are not the original EBU files.

## Formats

| Format         | Decoder                                                              | Windows      |
| -------------- | -------------------------------------------------------------------- | ------------ |
| WAV, FLAC      | built-in                                                             | 7 and later  |
| MP3, AAC (M4A) | Media Foundation                                                     | 7 and later  |
| ALAC (M4A)     | Media Foundation                                                     | 10 and later |
| Ogg Vorbis     | built-in (stb_vorbis)                                                | 7 and later  |
| Opus           | built-in (libopus 1.5.2), always 48 kHz                              | 7 and later  |
| DSD (DSF, DFF) | built-in; DSD64 / 128 / 256, up to 6 channels; DFF uncompressed only | 7 and later  |

## DSD

DSD stores only two values (+1 / −1), but 2.8 million times per second and more. A lowpass turns this into PCM. The plugin converts in two stages to 176.4 kHz (passband flat to 20 kHz within 0.00001 dB, stopband at least 124 dB). The spectrogram therefore reaches 88 kHz and shows the noise that DSD moves above the audible range.

Levels, loudness and DR are measured **in the hearing band**: after a 20 kHz lowpass at 44.1 kHz, as a DSD converter and foobar2000 do. The status line says "Levels in hearing band (20 kHz)". "Above 24 kHz" and the spectrum keep the full width.

Level reference as in ffmpeg and foobar2000: bits count as +1 / −1, no extra gain (some SACD players add 6 dB).

The filter design is in `notes/dsd_design.py`; it produces the coefficients in `src/dsdcoef.h`.

## Folder log

Right click → "Save folder log..." measures all music files in the folder of the displayed file. Other files in the folder are ignored. Files already measured come from the cache; the status line shows the progress.

Choose the file type in the save dialog:

- **CSV table** (`AudioSpectrogram.csv`): one line per file, every field in quotation marks, so commas in titles and the decimal comma do no harm. Opens as a table in spreadsheets and CSV Lister plugins.
- **Text report** (`AudioSpectrogram.txt`): one block per file with the same labels and values as the measurement panel, empty values left out, and the album DR at the end (mean of the rounded DR values, rounded, as in foobar2000).

The last choice is kept. The log contains measurements only, no rating and no ranking.

## Settings

Right click → Settings: playback, playback bar, language, text size, colour scheme, cache. Values are stored in `AudioSpectrogram.ini` in the plugin folder.

| Section     | Keys                                                                                         |
| ----------- | -------------------------------------------------------------------------------------------- |
| `[General]` | ClickToPlay, Language, LogFormat                                                             |
| `[Player]`  | Volume, Loop, AutoPlay, BarPosition, BarSize, BarWeight                                      |
| `[View]`    | Mode, SplitChannels, LogFrequency, ShowMeasurements, MarkDifferences, ColourScheme, TextSize |
| `[Cache]`   | Enabled, MaxSizeMB, Path                                                                     |

The cache holds format data, measurements and the average spectrum, about 13 KB per file, in `%LOCALAPPDATA%\AudioSpectrogram\Cache`. A file that changes (size or date) is measured again.

## Detect string

Total Commander decides with the detect string whether the plugin is used for a file. It is stored in `wincmd.ini`, section `[ListerPlugins]`. Default:

```
MULTIMEDIA & (EXT="MP3" | EXT="FLAC" | EXT="WAV" | EXT="M4A" | EXT="AAC" | EXT="OGG" | EXT="OGA" | EXT="OPUS" | EXT="DSF" | EXT="DFF")
```

To load the plugin only on request (Lister menu Options → Image/Multimedia, key 4), put `FORCE &` in front. To leave a file type to another viewer, remove its `EXT="..."` entry.

## System requirements

Windows 7 or later, Total Commander 32 or 64 bit. ALAC needs Windows 10.

The C runtime depends on the compiler used for the build:

- **MSVCRT**: part of every Windows since 7
- **UCRT**: part of Windows 10 and later; on Windows 7 only with update KB2999226

`build.bat` prints the runtime of each file it has built.

## Building from source

Requirements: MinGW-w64 (GCC), for example WinLibs. Adjust the compiler paths at the top of `build.bat`.

```
build.bat          release build, 64 and 32 bit, output in build\
debug_build.bat    same, but the plugin writes AudioSpectrogram_debug.log (output in build\debug\)
```

`build.bat` builds the Opus library once (`build\libopus64.a`, `build\libopus32.a`, about a minute) and reuses it. After changing the compiler, delete the folder `build` first.

The Opus sources in `src\third_party\opus` are the unchanged files of release 1.5.2 (https://opus-codec.org, tag v1.5.2); `config.h`, `sources.rsp` and `objects.rsp` were added for this build.

## Version history

**1.6.0**

- Folder log optionally as a text report in blocks, with album DR
- Time in the playback bar no longer jitters (digits of equal width)

**1.5.1**

- DSD files also in the folder log and with Page Up / Page Down
- Message "No music files in this folder" instead of a silent stop

**1.5.0**

- DSD: DSF and DFF, DSD64 / 128 / 256
- Levels, loudness and DR of DSD measured in the hearing band; spectrogram up to 88 kHz
- Detect string extended by `EXT="DSF" | EXT="DFF"`

**1.4.2**

- Folder log as CSV with quoted fields
- RMS referenced as in AES17 (full-scale sine = 0 dBFS), like foobar2000; crest factor unchanged
- Cutoff also found in upsampled material (second search); the cache is measured anew once

**1.4.1**

- Page Up / Page Down browse through the music files of the folder, also with only one file open; N / P keep following the selection in Total Commander
- Debug build with diagnostic log

**1.4.0**

- Playback bar above or below, size 100–200 %, weight light / semibold / bold
- Text size of the whole plugin 100–200 %
- Volume with Numpad + / − and Up / Down
- Optional auto play when a file is opened
- Status line shows overlap and time per image column

**1.1.0 – 1.3.3** (not published separately)

- Name corrected: AudioSpectogram → AudioSpectrogram
- Playback bar with play / pause, stop, loop and volume; Space starts playback
- Colour schemes Standard, Aurora, Viridis, Magma, Fire, Green, Grey
- Zoom in time and frequency with detail analysis (FFT up to 16384)
- Third-octave band view
- Encoder signature in the stream read completely (e.g. LAME3.99.5)

**1.0.0**

- First public version

## Licence and third-party code

AudioSpectrogram is released under the MIT licence, see `LICENSE.txt`.

Included:

- dr_flac, stb_vorbis (public domain / MIT)
- libopus 1.5.2 (BSD licence)
- viridis and magma colour scales from matplotlib (CC0)

Details in `THIRD_PARTY.txt`.



- Version: **1.6.0**
- Platforms: Total Commander 32 and 64 bit, Windows 7 or later
- Licence: MIT
- Author: Native2904
