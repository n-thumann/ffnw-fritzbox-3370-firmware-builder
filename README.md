# ffnw-fritzbox-3370-firmware-builder

This repository holds the Earthfile to build the Gluon firmware for [Freifunk Nordwest](https://git.ffnw.de/ffnw-firmware/siteconf) for AVM FRITZ!Box 3370 including a setup mode button [patch](https://github.com/freifunk-gluon/gluon/pull/2056/commits/11a235876060c06c088794b2bf4e0f6d89f1e5a8).

## Build

1. Install [Earthly](https://earthly.dev/)
2. Clone this repository and navigate into it
3. Gather required parameters for from https://git.ffnw.de/ffnw-firmware/siteconf/-/tags
4. Run e.g. `earthly +build --ffnw_tag rc/20220608 --ffnw_release 20220608 --gluon_release v2021.1.x --gluon_commit 595abcf8cb2dc794801c6ed5f1641783fccf5806`

The resulting image will be placed inside `./images`.

## Requirements

- ~10 GB of free disk space (and e.g. Docker Desktop being able to use it)
- ~60 min. of time (depending on your machine)
