# Mechen M30 community firmware

Unofficial community firmware for the Mechen M30 digital audio player. This
repository contains installable release images; the reverse-engineering,
source reconstruction, build tools, and tests live in the parent research
project.

See [CHANGELOG.md](CHANGELOG.md) for release-to-release changes.

## Current release

| Field | Value |
|---|---|
| Release | `V1.203.01` |
| Displayed build date | `2026-08-11` |
| Stock base | Mechen `2025-06-26`, displayed as `V1.101.10` |
| Install image | `releases/v1.203.01/MECHEN_M30.HEX` |
| Image SHA-256 | `0bd5e98892f3bd674fd392209f2c034b0807a4556d605edc35b7d960fd6743cb` |
| Source commit | `d99637c` (`Release V1.203.01 album-art regression hotfix`) |

The `.HEX` file is an encrypted Actions Semiconductor firmware-update
container, not an Intel HEX text file.

## Included bug fixes

V1.203.01 adds a bounded FLAC metadata-block scan so artist/album comments are
found even when a large PICTURE block places them beyond the stock 8 KiB
window. It is also a hotfix for rejected V1.203.00: that candidate wrote a
valid decoded cover cache but its new reader guard suppressed all
playing-screen artwork. V1.203.01 restores the complete accepted V1.202.03
reader byte-for-byte. Hardware testing confirmed that artwork returned and
indexed metadata remained functional.

It retains V1.202.03's stock fixed-array 4,000-track architecture and removal
of the rejected fixed-allocation database-zeroing patch. `M30-STATIC-012`
remains open; this release makes no claim that unused database ranges are zero
or free of prior FAT-cluster data.

It retains the six fixes introduced on the V1.200/V1.201 release line:

1. **CUE resolver initialization (`M30-STATIC-007`)** — clears the metadata
   structure and supplies bounded title storage before parsing.
2. **Final-CUE-track backward seeking (`M30-STATIC-009`)** — computes the seek
   destination after replacing the absent next-track time with file duration.
3. **Favorite position zero (`M30-STATIC-011`)** — rejects zero instead of
   traversing up to 65,536 playlist records.
4. **Indexed-library recovery (`M30-FW-006`)** — removes the rejected paged
   builder and restores the stock 4,000-track pipeline. The test M30 indexed
   all 1,439 tracks and opened All Songs, Album, Artist/Author, and Genre.
5. **Decoder callback safety (`M30-STATIC-002`)** — initializes the previously
   indeterminate decoder write callback to a defined function returning `-1`.
6. **ASRC coefficient-loader safety (`M30-STATIC-001`)** — commits a new
   coefficient set only after a successful seek, two exact reads, and close;
   incomplete loads fail silent and remain eligible for retry.

It also retains the fourteen V1.101.12 fixes, including:

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

The build changes six of the 99 inner firmware members relative to stock.
Relative to V1.202.03, only `music.ap` and the versioned `setting.ap` change.
The `music.ap` delta is exactly 112 byte positions in the bounded FLAC finder;
all three rejected V1.203.00 cache-reader regions are byte-identical to
V1.202.03. Every application module retains its original size, header, segment
table, bank table, and fixed allocation.

V1.201.00 retains the V1.200.01 correction for a regression in
V1.101.14/V1.200.00: pressing a non-power key during playback with the display
off could freeze the player. The hotfix removed the VM-backed configurable-lock
selector and restored the exact stock strict-lock key path in all seven
application overlays. The locked-controls Settings entry remains absent.

## Installation

> [!CAUTION]
> This is unofficial firmware. A failed update may require opening the player
> and using an external SPI programmer. Install only on a Mechen M30 already
> running the matching `2025-06-26` firmware family.

1. Fully charge the player and use a known-good FAT32 SD card.
2. Copy `releases/v1.203.01/MECHEN_M30.HEX` to the card root.
3. Ensure it is the only `.HEX` update image on the card.
4. Verify its SHA-256 against `releases/v1.203.01/SHA256SUMS`.
5. Open **Settings → Auto Upgrade** and do not interrupt the update.
6. Confirm version `V1.203.01` and date `2026-08-11` after reboot.

V1.101.14 and V1.200.00 were accepted through Auto Upgrade on one Mechen M30,
but later repeatable testing exposed their screen-off key regression. They are
superseded and should not be installed. V1.200.01 corrected the regression and
passed the complete screen-off, power-wake, and screen-on hardware test.
V1.201.00 subsequently booted and passed ordinary playback testing on the same
player, confirming the successful ASRC-loading path on hardware. V1.202.00 is
superseded: file-list generation repeatedly stopped at 50% and omitted
`M3U.LIB`. V1.202.01 corrected two overlay defects but failed again: an
existing card stopped at 50%, while a fresh card stopped at 10% and produced
no database files. V1.202.02 removes the overlay and reaches 100%, but its
captured `MUSIC.LIB` contains 1,439 linked records behind a finalized zero-count
header. The custom paged builder therefore failed after scanning, and indexed
Music views are unusable. V1.202.02 is withdrawn. V1.202.03 removes that
builder; hardware regeneration indexed all 1,439 tracks and restored All Songs,
Album, Artist/Author, and Genre. V1.203.00 is also withdrawn: its FLAC metadata
fix worked, but its cache-reader guard suppressed playing-screen art. V1.203.01
removes that guard and passed the scoped artwork/metadata regression test.

After installation, start playback, let the display turn off, and press every
non-power key individually. Each key must follow the stock locked behavior
without freezing. Verify power-button wake separately, then repeat the same
controls with the display on.

First test ordinary start, pause/resume, seek, track change, repeat modes,
screen-off controls, and stop. For a regeneration test, use a freshly
player-formatted card with at least one known-supported audio file and preserve
the complete input inventory plus regenerated databases. Generation must reach
100%; `MUSIC.LIB`, `M3U.LIB`, `ALBUM.PIC`, and all three `USERPL*.PL` files must
exist. Do not expect unused ranges to be zero.

## Verification

- Five V1.203.01 composition tests and the inherited component regressions
  pass.
- The complete 99-member FWIMAGE rebuild changes only the six declared
  members and authenticates all six source providers.
- Every replacement module is baseline-hash pinned and preserves its original
  module size and load layout.
- Native FWU verification decrypts the image to AFI SHA-256
  `9e99d4623edd5cc8f52e25900ed889bb293f1686fa7456b153281f8a2a044b72`.
- Rockbox `atjboottool` independently decrypts the same image to a
  byte-identical AFI.
- Inner FWIMAGE SHA-256:
  `24ec723633222c78d4a09494d4ebef485653a6355d091ae3b4b8ba0e9ade3575`.

Software verification does not replace device testing across codecs, board
revisions, SD cards, and failure conditions.

## TODO for the next version

Future ordinary releases increment the middle field and reset the final field:
`V1.203.00`, `V1.204.00`, and so on. The final field is reserved for an
exceptional hotfix or rebuild on the same release line.

### Integration targets

- [ ] `M30-FW-012`: profile and reduce the still-separate two-pass library
  rebuild time; V1.201.00 accelerates navigation and bounds index memory.
- [ ] `M30-FW-014`: reproduce the playing-versus-paused shutdown/battery-drain
  failure and add bounded recovery only at the confirmed stalled stage.
- [ ] Residual `M30-FW-004`: define and test one consistent filename, title,
  disc, track, Unicode, and copy-order policy beyond the two corrected numeric
  defects.
- [ ] Residual `M30-FW-005`: run the nine-case artwork matrix, distinguishing
  baseline/progressive JPEG, PNG, dimensions, tag version, embedded art, and
  external `Folder.jpg`; then change the decoder only where hardware evidence
  identifies a safe boundary.
- [ ] Residual `M30-FW-015`: run the long-string device matrix, then address
  remaining UI/codepage failures without increasing fixed buffers in place.
### Requires more research before a safe patch

- [ ] `M30-STATIC-012`: replace the rejected foreground database-zeroing loop
  with bounded asynchronous initialization, a filesystem clear primitive, or
  post-generation cleanup with explicit progress and rollback.
- [ ] `M30-FW-013`: reintroduce configurable locked controls only after finding
  a proven process-wide policy owner or a safe initializer for every application
  overlay; do not perform storage I/O from the screen-off key path.
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
