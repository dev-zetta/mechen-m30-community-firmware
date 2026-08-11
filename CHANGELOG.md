# Changelog

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
`atjboottool` to byte-identical AFI data. Hardware validation of every key at
screen-off is still required.

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
