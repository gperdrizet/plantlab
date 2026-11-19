# hugo

Raspberry Pi Zero W, plant monitoring node.

Named for Hugo de Vries (1848–1935), who examined 53,509 *Oenothera lamarckiana*
plants by hand until discrete heritable mutations revealed themselves.

## Hardware

- Raspberry Pi Zero W (ARMv6)
- SD card: <!-- brand/size -->

## OS

Raspberry Pi OS Legacy, 32-bit Lite (Bullseye)
Image: `2024-10-22-raspios-bullseye-armhf-lite.img.xz`

Bullseye required: Bookworm dropped ARMv6 support and will not boot on Pi Zero W.

## Setup

See [setup.md](setup.md) for the full flash and headless configuration procedure.

## Network

- Hostname: `hugo`
- Static IP: `10.1.10.143` (wired, USB ethernet dongle on eth0; wifi disabled)
- SSH: `ssh siderealyear@hugo` (alias in `~/.ssh/config`)
- Gateway: `10.1.10.1`, DNS `75.75.75.75` / `75.75.76.76`

Wired is required: the Pi Zero W's built-in wifi has no antenna through the
building, so usb-ethernet is the primary link. Static IP is set in
`/etc/dhcpcd.conf` (added at first-boot setup); `wlan0` is `denyinterfaces`.

## Storage

- `/` — root on SD card (15 GB)
- `/mnt/arkk` — NFS mount of the arkk RAID (15 TB), persistent, `nofail`:

  ```
  10.1.10.202:/mnt/arkk /mnt/arkk nfs rw,hard,sync,noatime,nofail 0 0
  ```

  `hard,sync` matches how pyrite mounts the same share; `nofail` lets hugo
  boot even if arkk is down. Code and data are staged on and off via this mount.

## Swap

None. The stock `dphys-swapfile` (which wrote to the SD card) is disabled; the
box idles around 44 MB used and a monitoring daemon will not come close to a
429 MB limit. Prefer an OOM over SD swap wear on a single-writes card.
