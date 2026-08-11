# Changelog

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
