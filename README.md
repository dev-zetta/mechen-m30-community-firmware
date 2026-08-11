# Mechen M30 community firmware

Unofficial community firmware for the Mechen M30 digital audio player. This
repository contains installable release images; the reverse-engineering,
source reconstruction, build tools, and tests live in the parent research
project.

## Current release

| Field | Value |
|---|---|
| Release | `V1.101.11` |
| Displayed build date | `2026-08-11` |
| Stock base | Mechen `2025-06-26`, displayed as `V1.101.10` |
| Install image | `releases/v1.101.11/MECHEN_M30.HEX` |
| Image SHA-256 | `d2e276e0ae47c8f2c65bc6542369ad1691ce7abfa83471f2f8431b285d2d94c7` |
| Source commit | `0a7d9a2` (`Set M30 community firmware build date`) |

The `.HEX` file is an encrypted Actions Semiconductor firmware-update
container, not an Intel HEX text file.

## Included bug fixes

This release integrates eight independently guarded fix sets:

1. **Metadata safety and compatibility (`M30-FW-003`, `M30-FW-005`,
   `M30-FW-015`)** — bounds Ogg, FLAC, MP3, WMA/ASF, AA, AAX/M4A, and APE
   metadata parsing; repairs shared stream-reader bounds; normalizes
   big-endian ID3 text; and accepts canonical four-byte UTF-8 while emitting
   complete UTF-16 surrogate pairs.
2. **CUE Repeat Folder (`M30-FW-010`)** — makes Repeat Folder wrap the virtual
   tracks in a CUE session instead of falling through to the Off behavior.
3. **Faster long-list navigation (`M30-FW-012`)** — caches exact list positions
   for the visible rows so scrolling does not repeatedly rescan the same
   thousands of entries.
4. **CRLF and LF M3U support (`M30-FW-007`)** — recognizes both Windows and
   Unix playlist line endings.
5. **Unterminated final M3U entry (`M30-FW-007`)** — retains the last pathname
   when a playlist does not end with CR or LF.
6. **Decimal track-number fallback (`M30-FW-004`)** — parses two-digit values
   in the correct order, so track `12` no longer becomes sort key `21`.
7. **Unsigned track ordering (`M30-FW-004`)** — compares the stored one-byte
   track key as unsigned, keeping `128..254` and the no-track sentinel in their
   intended order.
8. **End-of-track ASRC drain (`M30-FW-001`)** — waits for downstream audio to
   drain before normal EOF shutdown, while preserving the stock timeout and
   abnormal-EOF fallback.

The build changes five of the 99 inner firmware members: four modules contain
the eight functional fixes, and `setting.ap` contains the version/date marker.
All other 94 members retain their exact allocated bytes.

## Installation

> [!CAUTION]
> This is unofficial firmware. A failed update may require opening the player
> and using an external SPI programmer. Install only on a Mechen M30 that is
> already running the matching `2025-06-26` / `V1.101.10` firmware family.

1. Fully charge the player and use a known-good FAT32 SD card.
2. Copy `releases/v1.101.11/MECHEN_M30.HEX` to the root of the card.
3. Ensure it is the only `.HEX` update image on the card.
4. Verify its SHA-256 against `releases/v1.101.11/SHA256SUMS`.
5. On the player, open **Settings → Auto Upgrade**.
6. Do not interrupt power or remove the card while the update is running.
7. After reboot, confirm version `V1.101.11` and date `2026-08-11` in the UI.

The earlier build with the same eight functional modules was accepted by Auto
Upgrade on one M30, booted normally, and displayed `V1.101.11`. The packaged
image differs from that tested image only in four displayed-date bytes and is
awaiting its own installation confirmation.

## Verification

- All 121 focused unit tests and associated host regressions pass.
- The complete 99-member FWIMAGE rebuild changes only the five declared
  members.
- Every replacement module is baseline-hash pinned and preserves its original
  module size and load layout.
- Native FWU verification decrypts the image to AFI SHA-256
  `e211c2ce778607399744300199c6e8449aefa18a1d4293079c34608f34fc9eec`.
- Rockbox `atjboottool` independently decrypts the same image to a
  byte-identical AFI.
- Inner FWIMAGE SHA-256:
  `699c4736e6d1d8b56d9c9636035bf5291f9de3495d11c2f22e0b7d285221a5b3`.

Software verification does not replace device testing across codecs, board
revisions, SD cards, and failure conditions.

## TODO for the next version

### Integration targets

- [ ] `M30-FW-006`: integrate and device-test the existing paged index for up
  to 10,000 tracks, including interrupted rebuild and database rollback.
- [ ] `M30-FW-013`: integrate a persistent user-selectable lock policy instead
  of requiring a firmware downgrade to change locked-screen controls.
- [ ] `M30-FW-012`: profile and reduce the still-separate two-pass library
  rebuild time; the current release accelerates navigation, not scanning.
- [ ] `M30-FW-014`: reproduce the playing-versus-paused shutdown/battery-drain
  failure and add bounded recovery only at the confirmed stalled stage.
- [ ] Residual `M30-FW-004`: define and test one consistent filename, title,
  disc, track, Unicode, and copy-order policy beyond the two corrected numeric
  defects.
- [ ] Residual `M30-FW-005` and `M30-FW-015`: run the artwork and long-string
  device matrices, then address remaining JPEG/UI/codepage failures without
  increasing fixed buffers in place.
- [ ] `M30-STATIC-003` through `M30-STATIC-006`: fix lyric index underflow,
  equality-boundary refresh, the 90-second final-label constant, and the
  one-byte label-buffer overflow after caller and device validation.
- [ ] `M30-STATIC-007` through `M30-STATIC-009`: initialize CUE metadata/time
  state, correct declared buffer capacities, and repair final-track backward
  seeking.
- [ ] `M30-STATIC-010` and `M30-STATIC-011`: preserve the computed SD-removal
  return action and reject favorite-playlist position zero.
- [ ] `M30-STATIC-012`: zero newly allocated `MUSIC.LIB`, `M3U.LIB`, and
  `ALBUM.PIC` ranges while measuring scan time, wear, and power-loss behavior.
- [ ] `M30-STATIC-001` and `M30-STATIC-002`: define safe failure behavior for
  ASRC coefficient I/O and the decoder's uninitialized write callback.

### Requires more research before a safe patch

- [ ] `M30-FW-002`: true gapless playback needs a second source or atomic PCM
  handoff; keeping the DAC open is insufficient.
- [ ] `M30-FW-008`: high-rate EQ is a DSP memory/cycle project, not a simple
  enable flag.
- [ ] `M30-FW-009`: identify the board/model capability initializer that gates
  FLAC above 48 kHz before bypassing it.
- [ ] `M30-FW-011`: design an album-art/lyrics preference and persistent state
  owner rather than hiding one feature unconditionally.
- [ ] `M30-FW-016`: isolate the reported AAC/M4A mono behavior; the recovered
  CPU path already requests stereo, leaving the proprietary AAC DSP/ABI as the
  likely owner.
- [ ] `M30-FW-017`: replace the official NOR service's three-byte W25Q256 read
  command with a four-byte read in recovery/update tooling and revalidate all
  lower- and upper-half operations.

Mechanical jack, USB connector, battery, display, wheel, and button failures
are hardware issues and are outside the firmware roadmap.

## Reporting results

Please include the displayed version/date, M30 board or USB revision, SD-card
format, exact test-file hash, playback path, repeat/EQ state, reproduction
steps, and any resulting `MUSIC.LIB`, `M3U.LIB`, or `ALBUM.PIC` captures.

## Legal and project status

This project is independent and is not affiliated with or endorsed by Mechen
or Actions Semiconductor. See `NOTICE.md` before redistributing the binary.
