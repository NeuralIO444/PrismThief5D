# References

Projects on the map around PrismThief5D. Neither of these changes the
spec's spine — the pull path stays gphoto2/libgphoto2 over USB on a
stock 5D Mark II — but each marks a deeper end of the territory.

## anuejn/2d5 — driving the sensor bare

https://github.com/anuejn/2d5

Reverse engineering the Canon 5D Mark II's image sensor itself: notes
and tools toward running the bare chip (full-frame, 36×24 mm,
5,616×3,744, ~€40 on the surplus market) outside the camera body.
Work in progress — by the repo's own statement, the sensor is not yet
usable that way.

Relevance: the moonshot shelf. If a bare 5D II sensor ever becomes
drivable, a scan head stops needing a camera body at all. Filed as
territory to watch, not a dependency.

## boscacci/magic-lantern-5d4-research — the firmware fight, documented

https://github.com/boscacci/magic-lantern-5d4-research

A paused research archive for Magic Lantern on the 5D Mark IV
(firmware 1.4.0): reproducible toolchains, source patches, QEMU work,
firmware-container analysis, and a plainly written record of the
remaining hardware blockers. Stated up front in the repo: there is no
working 1.4.0 build or installer, and its binaries are not to go on a
camera.

Relevance: twofold. Methodologically it's the closest cousin to our
tether layer — careful, bounded probing of a Canon over USB, with
exactly where the wall is written down. And it's the cautionary tale
for the firmware route generally: this is what "not boring" costs.
Note it targets the Mark IV; Magic Lantern for the Mark II is the
mature original — and this pipeline doesn't need either.
