# Mechen M30 community firmware

Unofficial community firmware for the Mechen M30 digital audio player. This
repository contains installable release images; the reverse-engineering,
source reconstruction, build tools, and tests live in the parent research
project.

See [CHANGELOG.md](CHANGELOG.md) for release-to-release changes.

## Current release

| Field | Value |
|---|---|
| Release | `V1.101.12` |
| Displayed build date | `2026-08-11` |
| Stock base | Mechen `2025-06-26`, displayed as `V1.101.10` |
| Install image | `releases/v1.101.12/MECHEN_M30.HEX` |
| Image SHA-256 | `e6ae7b672e86c1743d15a460a2e9987dc9f39f273b13825cb953f7f638e3ce3b` |
| Source commit | `f41bd2b` (`Build M30 community firmware V1.101.12 candidate`) |

The `.HEX` file is an encrypted Actions Semiconductor firmware-update
container, not an Intel HEX text file.

## Included bug fixes

This release integrates fourteen independently guarded fix sets. V1.101.12
adds the following six fixes:

1. **Safe lyric navigation (`M30-STATIC-003`)** — prevents Previous Lyric from
   decrementing index zero to `65535` and keeps the first lyric at index one.
2. **Exact lyric timestamp transition (`M30-STATIC-004`)** — refreshes the
   lyric when playback time equals the next timestamp instead of waiting for a
   later tick.
3. **Final lyric timeout (`M30-STATIC-005`)** — uses the intended 1.5-second
   final-label window instead of 90 seconds.
4. **Lyric buffer boundary (`M30-STATIC-006`)** — removes a redundant
   terminator write one byte beyond the caller-provided lyric buffer.
5. **CUE metadata capacities (`M30-STATIC-008`)** — reserves terminator space
   in the title, artist, and album buffers.
6. **SD-removal result (`M30-STATIC-010`)** — returns the action already
   computed by the handler rather than replacing it with a constant result.

It retains all eight V1.101.11 fixes:

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
the fourteen functional fixes, and `setting.ap` contains the version/date
marker. All other 94 members retain their exact allocated bytes.

## Installation

> [!CAUTION]
> This is unofficial firmware. A failed update may require opening the player
> and using an external SPI programmer. Install only on a Mechen M30 that is
> already running the matching `2025-06-26` / `V1.101.10` firmware family.

1. Fully charge the player and use a known-good FAT32 SD card.
2. Copy `releases/v1.101.12/MECHEN_M30.HEX` to the root of the card.
3. Ensure it is the only `.HEX` update image on the card.
4. Verify its SHA-256 against `releases/v1.101.12/SHA256SUMS`.
5. On the player, open **Settings → Auto Upgrade**.
6. Do not interrupt power or remove the card while the update is running.
7. After reboot, confirm version `V1.101.12` and date `2026-08-11` in the UI.

This exact V1.101.12 image was accepted through Auto Upgrade on one Mechen M30.
The player booted and operated normally after installation. This establishes
acceptance for that unit, not every board revision or failure condition.

## Verification

- All 129 focused unit tests and associated host regressions pass: 121 for the
  inherited V1.101.11 fixes and eight for the V1.101.12 additions/composition.
- The complete 99-member FWIMAGE rebuild changes only the five declared
  members.
- Every replacement module is baseline-hash pinned and preserves its original
  module size and load layout.
- Native FWU verification decrypts the image to AFI SHA-256
  `ee0965140da1515f64e5ecb98f8d60447b37b01f2e206b67a165297d99a32397`.
- Rockbox `atjboottool` independently decrypts the same image to a
  byte-identical AFI.
- Inner FWIMAGE SHA-256:
  `1926b3268499b0e92af234d0d5aec1aee3957dfcc2baf1afebb3a42b503eea1b`.

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
- [ ] `M30-STATIC-007` and `M30-STATIC-009`: initialize CUE metadata/time state
  and repair final-track backward seeking.
- [ ] `M30-STATIC-011`: reject favorite-playlist position zero.
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
