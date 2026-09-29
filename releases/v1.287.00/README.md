# Mechen M30 community firmware V1.287.00

V1.287.00 is a hardware-tested baseline-JPEG album-art compatibility release. On one Mechen M30, all five pinned 30-second controls displayed covers and started playback: 640x361 and 641x361 baseline 4:2:0, 641x361 4:4:4, 641x361 4:2:2, and 641x361 grayscale.

Known limitation: the first play of each tested track took approximately three to five seconds before its cover appeared and playback began. Artwork work remains synchronous in the pre-play path. Progressive JPEG, PNG, fallback JPEG payloads larger than 44,800 bytes, other containers/codecs, malformed inputs outside the bounded tests, and other M30 revisions are not established by this result.

Install only through **Settings -> Auto Upgrade** on a fully charged Mechen M30 from the matching `2025-06-26` firmware family. Copy `MECHEN_M30.HEX` to the root of a player-formatted card, ensure it is the only `.HEX` updater, verify SHA-256 `d75f08e1e050e4e84bed1a64e9b66e595cb435958900b7d576b817e02ff47d36`, and do not interrupt the update. After reboot, confirm `V1.287.00` and date `2026-09-29`.

This is unofficial firmware. A failed update may require hardware recovery with an external SPI programmer.
