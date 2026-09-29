# Mechen M30 community firmware V1.292.00

V1.292.00 is the hardware-accepted recovery and current release of the bounded baseline-JPEG album-art fallback. Its executable `music.ap` is byte-identical to hardware-accepted V1.287.00; only the displayed version changes in `setting.ap`.

On one Mechen M30, the direct V1.292 recovery controls both displayed their album covers and remained responsive. Stock-compatible 640x361 baseline 4:2:0 case 20 began almost immediately. Fallback 641x361 baseline 4:2:0 case 11 began after approximately four seconds. The identical V1.287 Music payload had previously passed the complete five-case matrix covering 4:2:0, 4:4:4, 4:2:2, and grayscale.

Known limitation: unsupported artwork handled by the CPU fallback can delay playback by approximately four seconds because decoding remains synchronous before PLAY. Progressive JPEG, PNG, fallback JPEG payloads larger than 44,800 bytes, other containers/codecs, malformed inputs outside the bounded tests, and other M30 revisions remain unverified.

Install only through **Settings -> Auto Upgrade** on a fully charged Mechen M30 from the matching `2025-06-26` firmware family. Copy `MECHEN_M30.HEX` to the root of a player-formatted card, ensure it is the only `.HEX` updater, verify SHA-256 `5e17d883f697a67d4face6f624ddc30952c5542c918b219be974bf380383ed2c`, and do not interrupt the update. After reboot, confirm `V1.292.00` and date `2026-09-29`.

This is unofficial firmware. A failed update may require hardware recovery with an external SPI programmer.
