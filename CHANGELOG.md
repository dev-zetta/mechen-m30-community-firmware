# Changelog

## V1.292.00 — 2026-09-29

Hardware-accepted recovery release. V1.292.00 restores the exact V1.287.00 `music.ap` after V1.288 through V1.291 research builds rejected post-PLAY fallback execution. Only two displayed-version bytes change in `setting.ap` relative to V1.287; the fallback implementation and every other FWIMAGE member remain byte-identical.

On one Mechen M30, direct case-20 and case-11 recovery tests both displayed their covers and remained responsive. Case 20 began almost immediately. Case 11 began after approximately four seconds, retaining the known synchronous CPU-fallback delay. V1.287's identical Music module had already passed the complete five-case baseline-JPEG matrix covering 4:2:0, 4:4:4, 4:2:2, and grayscale.

The encrypted image SHA-256 is `5e17d883f697a67d4face6f624ddc30952c5542c918b219be974bf380383ed2c`. Native and Rockbox decryption produce the same AFI SHA-256 `66e46b5e16d133091a0cf8102ea2a07383d77a8aa33e0393c3b27129ce0e79de`; the inner FWIMAGE SHA-256 is `c87035fa32bd83a2c2d36f5564ff4ab049d3f8cdc3ec9a2a75a8eb7a4cb7d745`.

## V1.287.00 — 2026-09-29

Hardware-accepted baseline-JPEG album-art compatibility release. A bounded scale-3 CPU fallback now runs only after complete APIC spooling, stock decoder failure, and stock cleanup. It accepts baseline one- and three-component JPEGs, covers 4:2:0, 4:2:2, 4:4:4, and grayscale, caps compressed input at 44,800 bytes and output at 150x150 RGB565, bounds callbacks/MCUs, and rejects output that could overlap unread compressed input. The ordinary stock-success path remains unchanged.

On one Mechen M30, all five pinned 30-second controls displayed covers and started playback: stock-compatible 640x361 4:2:0, fallback 641x361 4:2:0, 641x361 4:4:4, 641x361 4:2:2, and 641x361 grayscale. Every case took approximately three to five seconds before cover and playback. That synchronous first-play delay is a known limitation. Progressive JPEG, PNG, fallback payloads above 44,800 bytes, other codecs/containers, malformed cases beyond the bounded suite, and other hardware revisions remain unverified.

The encrypted image SHA-256 is `d75f08e1e050e4e84bed1a64e9b66e595cb435958900b7d576b817e02ff47d36`. Native and Rockbox decryption produce the same AFI SHA-256 `13c6a3b60c54d1d29cbe2684d66adbbd2c034019f333d3cb634166090563cc89`; the inner FWIMAGE SHA-256 is `7cb1f94fb8fbf10623cb67b0c4f0109a916fb93d6cd37e54c676e3d2524cb327`.

## V1.203.01 — 2026-08-11

Hardware-accepted FLAC metadata and album-art regression hotfix. The bounded
FLAC block scan follows valid metadata-block lengths instead of giving up at
the stock 8 KiB window, fixing artist/album fallback when a large PICTURE block
precedes VORBIS_COMMENT.

V1.203.00 proved that scan on hardware, but its independent strict-marker and
geometry guard suppressed all Playing-screen artwork even though the player
wrote a valid, nonblank `ALBUM.PIC`. V1.203.01 removes all 80 reader-change
byte positions and restores those regions byte-for-byte from V1.202.03 while
retaining the 112-byte-position FLAC finder change. The test player booted,
retained working indexed metadata, and displayed artwork again. V1.203.00 is
withdrawn and should not be installed.

The encrypted image SHA-256 is
`0bd5e98892f3bd674fd392209f2c034b0807a4556d605edc35b7d960fd6743cb`.
Native and Rockbox decryption produce the same AFI SHA-256
`9e99d4623edd5cc8f52e25900ed889bb293f1686fa7456b153281f8a2a044b72`.

## V1.202.03 — 2026-08-11

Hardware-accepted indexed-library rollback. V1.202.02 scanned 1,439 tracks but
its inherited 10,000-track paged builder failed afterward and finalized a
zero-count `MUSIC.LIB`, leaving All Songs, Album, Artist/Author, and Genre
empty. Folder playback bypassed those views and masked the regression.

V1.202.03 removes the paged builder, restores the stock fixed-array 4,000-track
architecture and `0x281000` `MUSIC.LIB` allocation, and restores all ten stock
consumer caps. The M3U, track-order, metadata, CUE/favorite, screen-off lock,
decoder callback, track-end drain, and ASRC fixes remain. On the test M30, a
clean regeneration indexed all 1,439 tracks and every indexed Music category
opened correctly.

The encrypted image SHA-256 is
`de2660cea96d509a275c76e3beb05aab9bd2840b7c7a9e736e179a9d5ee255c6`.
Native and Rockbox decryption produce the same AFI SHA-256
`d07d7489c9ff368b43bb9bf0aabd8f7f0d84457f685446245405327eab57eacd`.

## V1.202.02 — 2026-08-11

**Withdrawn. Do not install:** later indexed-view testing showed Album,
Artist, Genre, and All Songs as empty. The captured `MUSIC.LIB` body contains
1,439 correctly linked records while its finalized header reports zero tracks,
proving that the inherited custom paged builder failed after a successful
scan. Folder playback bypassed the broken views and masked the regression.
V1.202.03 restores the stock 4,000-track index pipeline.

Corrective rollback for the V1.202.00/V1.202.01 database-generation
regression. V1.202.01 installed byte-exactly but still stopped at the first
missing fixed-allocation database: 50% without `M3U.LIB` on an existing card,
and 10% with no databases on a fresh card.

V1.202.02 removes all 352 byte positions belonging to the synchronous
sector-zeroing overlay and restores the byte-exact V1.201.00 `playlist.ap`.
All twenty inherited functional patch sets remain, including the paged index
that this later test rejected; only the unresolved `M30-STATIC-012` privacy
patch is removed. The displayed version is
`V1.202.02`; the build date remains `2026-08-11`.

The complete inherited suite, authenticated 99-member rebuild, rollback tests,
native encrypted-update verification, and independent Rockbox decryption pass.
On the test M30, file-list generation reached 100% and created structurally
valid `MUSIC.LIB`, `M3U.LIB`, `ALBUM.PIC`, and three initialized `USERPL*.PL`
files at the expected sizes. Subsequent body analysis proved that 1,439 tracks
had been scanned before the paged builder erased the logical count, so reaching
100% did not produce a usable index.

## V1.202.01 — 2026-08-11

**Superseded by V1.202.02. Do not install:** the corrected overlay still failed
hardware regeneration. This was intended as a hotfix for V1.202.00 stopping without
creating `M3U.LIB`. The V1.202.00 zeroing helper saved the UTF-16LE pathname
before reusing the shared sector buffer, but failed to restore it before the
unchanged stock close/reopen continuation. Its separate album adapter also
constructed `0x0f400` rather than the stock `0x1f400` allocation.

The corrected helper restores the pathname after its final rewind, and the
album adapter now passes the exact stock size. Relative to V1.202.00, only
`playlist.ap` and one version byte in `setting.ap` change; the playlist delta
is 111 byte positions. Existing database files remain on the unchanged path.

The complete inherited suite, corrected host regression, all-release guards,
six-provider 99-member rebuild, native encrypted-update verification, and
independent Rockbox decryption pass. Fresh three-file generation on hardware
remains the acceptance test.

## V1.202.00 — 2026-08-11

**Superseded by V1.202.02. Do not install:** hardware file-list generation
repeatedly stopped at 50% and omitted `M3U.LIB`.

Adds guarded zero-initialization for newly allocated `MUSIC.LIB`, `M3U.LIB`,
and `ALBUM.PIC` files (`M30-STATIC-012`) over the hardware-accepted V1.201.00
payload. Stock preallocates each file at its complete fixed size but initializes
only headers and live records, allowing unused ranges to expose prior FAT-
cluster contents through ordinary file access.

The replacement saves the filename, zeroes sector zero first and every
remaining 512-byte sector, rewinds the handle, and then resumes the unchanged
stock builder. Any seek or write failure closes and removes the partial full-
size file. Existing database files do not enter the hook and receive no extra
writes. A complete three-file recreation adds at most 5.60 MiB of SD-card
writes and does not touch the player's internal NOR.

Five guarded `playlist.ap` ranges change 334 byte positions and are disjoint
from every inherited patch. The complete inherited release suite, new host
fault matrix, all-release byte guards, six-provider build, native encrypted-
update verification, and independent Rockbox decryption all pass. Hardware
database recreation, timing, and unused-range capture remain acceptance tests.

## V1.201.00 — 2026-08-11

Adds guarded ASRC coefficient loading over the hardware-accepted V1.200.01
payload. Stock records a requested coefficient set as loaded after any
successful `coeffi.bin` open, even when seek, either read, or close fails.

The replacement accepts only the six physical sets, requires successful seek
and close plus exact `0x0e00`- and `0x0a0c`-byte reads, and updates loaded state
only on complete success. Any failure disables the DAC and preserves the old
loaded-set marker so the next start retries instead of using partially replaced
coefficients. Successful loading and coefficient data are unchanged.

The ASRC overlay changes 190 byte positions in `mengine.ap` and is disjoint
from the existing end-drain and decoder-callback fixes. The complete inherited
suite, nine ASRC tests, six release-integration tests, native update verification,
and independent Rockbox decryption all pass. The release subsequently booted
and passed ordinary playback testing on the test M30, validating the successful
coefficient-loading path on hardware; deliberate resource-failure injection
remains host-only.

## V1.200.01 — 2026-08-11

Hotfix for the repeatable V1.101.14/V1.200.00 screen-off key freeze. While
music was playing with the display off, pressing any non-power key could leave
the player unresponsive until restart; screen-on controls and the power-key
wake path were unaffected.

The configurable locked-controls feature is disabled. V1.200.01 removes its
VM read/write callbacks, Settings menu/resources, runtime helper, and all seven
application hooks. Each application now contains the exact stock strict-lock
hook and zero reserve, avoiding storage access in the key path. The other
nineteen guarded fix sets remain included.

The complete inherited test suite and five hotfix-specific tests pass. The
encrypted image decrypts through both the native verifier and Rockbox
`atjboottool` to byte-identical AFI data. The test M30 subsequently passed every
screen-off non-power key, separate power wake, and screen-on control check
without freezing.

## V1.200.00 — 2026-08-11

**Superseded by V1.200.01. Do not install:** later repeatable hardware testing
found the screen-off non-power-key freeze described above.

First release under the community firmware's minor-version scheme. Future
ordinary releases advance the middle field (`V1.201.00`, `V1.202.00`, ...);
the final field is reserved for exceptional hotfix rebuilds.

The exact functional payload was hardware-tested as the V1.101.14 candidate.
V1.200.00 changes only the displayed version marker and passes the complete
167-test release and dual-decryption gate.

Added six guarded fixes over V1.101.12:

- support up to 10,000 tracks in indexed Music views with bounded paged
  indexing and coordinated scanner/consumer limits;
- add a persistent Settings selector for strict or transport-enabled locked
  controls;
- initialize the decoder write callback to a defined rejecting function;
- initialize CUE resolver metadata and bounded scratch storage;
- repair backward seeking from the final CUE track;
- reject favorite-playlist position zero without a 65,536-record traversal.

All fourteen V1.101.12 fixes remain included.

## V1.101.12 — 2026-08-11

Hardware accepted on a Mechen M30 through the player's **Settings → Auto
Upgrade** path. The player booted and operated normally after installation.

Added six guarded fixes:

- prevent Previous Lyric from underflowing at lyric index zero;
- advance lyrics when playback time exactly equals the next timestamp;
- reduce the final-lyric display window from 90 seconds to 1.5 seconds;
- remove a one-byte lyric-label buffer overflow;
- reserve terminator space in CUE title, artist, and album buffers;
- return the action computed by the SD-removal handler.

The eight V1.101.11 fixes remain included. V1.101.12 differs from V1.101.11
only in `music.ap` and the `setting.ap` version marker.

## V1.101.11 — 2026-08-11

Initial community release with eight guarded fix sets:

- bounded metadata and artwork parsing, UTF-8/UTF-16 and ID3 corrections;
- CUE Repeat Folder behavior;
- cached long-list navigation;
- CRLF/LF and unterminated-final-line M3U parsing;
- decimal track-number and unsigned ordering corrections;
- end-of-track ASRC drain gating.
