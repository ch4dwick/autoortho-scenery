# autoortho-scenery
Prepared X-Plane scenery packs for the autoortho project

This is a fork and refactor kubilus1's repository so it will build with a newer environment.

# Local Build

Requirements
- This repository was tested on Ubuntu 24.04.3 LTS
- sudo apt update && sudo apt-get install -y python3 python3-pip python3-tk zip libgeos-dev


```bash
# If you want to do venv, otherwise, skip the 2 commands
python3 -m venv ao-scenery
source ao-scenery/bin/activate
pip3 install certifi chardet idna numpy Pillow pyproj requests Rtree Shapely urllib3 scikit-fmm
```

Your environment should be ready to pull orthos.

# Tiles

Run Main Build via cli:

TILENAME - This the region of tiles you want to download. Valid values are: asi afr sa na aus_pac eur test. Default: test.

```bash
make Ortho4XP
export TILENAME=test
CHUNKS=$(ls ${TILENAME}_tile_list.* | awk -v TILENAME=${TILENAME} -F '.' '/.+/ { printf "%s\"z_" TILENAME "_%s.zip\"", sep, ""$2""; sep=", " }')

make -f Makefile.tiles ${TILENAME}_tile_list_chunks
make -f Makefile.tiles all
```

**WARNING**: All regions except 'test' are long running, bandwith and storage consuming processes. Make sure your home set up can handle it. Ortho provider may also throttle your downloads.

- Downloads Ortho4XP
- Generates initial tile chunks so you know how many zip files it generates.
- Downloads the tiles for packaging.
- Packages downloaded tiles into the a zip prefixed with the region.

# Overlays

Run Main Build via cli, assuming you want to run a test overlay:

OVERLAYNAME = This the region of overlays you want to download. Valid values are: asi afr sa na aus_pac eur test. Default: test. 

## Download Overlay

Update Ortho4XP.cfg key custom_overlay_src to your local X-Plane 12 Global Scenery folder found in <Your X-Plane 12 folder>\Global Scenery\X-Plane 12 Global Scenery

```bash
OVERLAYNAME=test make -f Makefile.overlay all
```

**WARNING**: All regions except 'test' are long running, bandwith and storage consuming processes. Make sure your home set up can handle it. Ortho provider may also throttle your downloads.

- installs the latest Ortho4XP
- copies our custom configuration
- copies overlay extract script
- downloads and saves the overlay under Ortho4XP/yOrtho4XP_Overlays
- collates them under ./y_(OVERLAYNAME)