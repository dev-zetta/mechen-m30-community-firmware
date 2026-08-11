# Changelog

## V1.202.02 — 2026-08-11

Corrective rollback for the V1.202.00/V1.202.01 database-generation
regression. V1.202.01 installed byte-exactly but still stopped at the first
missing fixed-allocation database: 50% without `M3U.LIB` on an existing card,
and 10% with no databases on a fresh card.

V1.202.02 removes all 352 byte positions belonging to the synchronous
sector-zeroing overlay and restores the byte-exact V1.201.00 `playlist.ap`.
All twenty previously accepted functional fix sets remain; only the unresolved
`M30-STATIC-012` privacy patch is removed. The displayed version is
`V1.202.02`; the build date remains `2026-08-11`.

The complete inherited suite, authenticated 99-member rebuild, rollback tests,
native encrypted-update verification, and independent Rockbox decryption pass.
On the test M30, file-list generation reached 100% and created structurally
valid `MUSIC.LIB`, `M3U.LIB`, `ALBUM.PIC`, and three initialized `USERPL*.PL`
files at the expected sizes. The captured music database contained zero tracks,
so this run validates database creation but not non-empty audio indexing.

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
