# autoortho-scenery
Prepared X-Plane scenery packs for the autoortho project

This is a fork and refactor kubilus1's repository so it will build with a newer environment.

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

```bash
OVERLAYNAME=test make -f Makefile.overlay 'y_test/yOrtho4XP_Overlays/*/*/+00-051.dsf'
```
Note: The cooridinates at the end is the region you want to the script to generate.

- installs the latest Ortho4XP
- copies our custom configuration
- copies overlay extract script
- downloads and saves the overlay under Ortho4XP/yOrtho4XP_Overlays
- collates them under ./y_(OVERLAYNAME)

## Package the overlay

```bash
OVERLAYNAME=test make -f Makefile.overlay 'y_test_overlays.zip'
```
Generates a zip file based overlay you just downloaded.