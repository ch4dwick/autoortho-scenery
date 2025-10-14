# autoortho-scenery
Prepared X-Plane scenery packs for the autoortho project

This is a fork and refactor kubilus1's repository so it will build with a newer environment.

# Local Build

Requirements
- This repository was tested on Ubuntu 24.04.3 LTS
- sudo apt update && sudo apt-get install -y g++ 7zip pkg-config python3 python3-pip python3-tk zip libgeos-dev


```bash
# If you want to do venv, otherwise, skip the 2 commands
python3 -m venv ao-scenery
source ao-scenery/bin/activate
pip3 install setuptools certifi chardet idna numpy Pillow pyproj requests Rtree Shapely urllib3 scikit-fmm
```

Your environment should be ready to pull orthos.

# Tiles

Run Main Build via cli:

TILENAME - This the region of tiles you want to download. Valid values are: asi afr sa na aus_pac eur test. Default: test.

```bash
export TILENAME=test

make -f Makefile.tiles ${TILENAME}_tile_list_chunks
make -f Makefile.tiles -j $(nproc --ignore=2) all
```

**WARNING**: All regions except 'test' are long running, bandwith and storage consuming processes. Make sure your home set up can handle it. Ortho provider may also throttle your downloads.

- Generates initial tile chunks so you know how many zip files it generates.
- Downloads the tiles for packaging.
- Packages downloaded tiles into the a zip prefixed with the region.

Once done, move the newly created tiles from autoortho-scenery/z_\<your region>/* to \<Your xplane install folder>/Custom Scenery/z_autoortho/scenery/z_ao_<your region>. Move or copy the CONTENTS and not the folder itself.

# Overlays

Run Main Build via cli, assuming you want to run a test overlay:

OVERLAYNAME = This the region of overlays you want to download. Valid values are: asi afr sa na aus_pac eur test. Default: test. 

## Download Overlay

Update Ortho4XP.cfg key custom_overlay_src to your local X-Plane 12 Global Scenery folder found in <Your X-Plane 12 folder>\Global Scenery\X-Plane 12 Global Scenery

```bash
OVERLAYNAME=test make -f Makefile.overlay -j $(nproc --ignore=2) all
```

**WARNING**: All regions except 'test' are long running, bandwith and storage consuming processes. Make sure your home set up can handle it. Ortho provider may also throttle your downloads.

- installs the latest Ortho4XP
- copies our custom configuration
- copies overlay extract script
- downloads and saves the overlay under Ortho4XP/yOrtho4XP_Overlays
- collates them under ./y_(OVERLAYNAME)

# [hotbso](https://github.com/hotbso/o4xp_2_xp12) Script for Seasons

As is, the AO Scenery tiles don't respond to the new XP12 seasons. hotbso has created a script that updates Ortho tiles data with seasons.

Typical o4xp_2_xp12.ini config that I used to update:
```
[DEFAULTS]
xp12_root = C:\path to\X-Plane 12
ortho_dir = C:\path to\X-Plane 12\z_autoortho\scenery
work_dir = C:\path to\X-Plane 12\tmp
num_workers = 14

[TOOLS]
```

You can convert the entire scenery folder or try out a region. 

To convert the entire folder:
> o4xp_2_xp12_ubuntu-latest convert

To only update a region's tiles:
> o4xp_2_xp12_ubuntu-latest -subset z_ao_eur convert
Make sure that region's folder exists before executing.

# Known issues
Due to Ortho4XP's dependency on X-Plane's global scenery, the tiles created may have issues if you don't configure Ortho4XP to use it on build.

Script doesn't take into consideration a modular build. Meaning if you re-ran the current Ortho4XP state after a successful build and then proceeded to create one for a different region, that build will contain tiles from the previous one. i.e. The zips will be an iteration of the prevoius one. A workaround I have practiced was to rename the Ortho4XP from one batch (e.g. Ortho4XP_asi) and pull a fresh copy of Ortho4XP, leaving the older one containing one region intact.

SPLITSIZE is optional for local builds as it was primarily used for github action's async build pipeline. If you're buiding this locally, there is no need to zip the finished folder as you only need to copy or move this to your AO folder. To avoid this script from generating multiple chunk files, just set split size to a significantly higher number, like, 50000 or something.

## Performance

This and hotbso's script are I/O intensive, avoid using this on a hard drive if possibe, otherwise, it would take you not only hours but days to complete an entire region's workflow.

During my tests, this script was used WSL on Windows 11 to build the tiles. While ao-scenery performed significantly faster, hotbso's script was dismally slow. If you are experiencing this, I recommend you move the results outside of WSL and run the Windows version of hotbso' script. Details on what to copy are defined [here](https://github.com/ch4dwick/autoortho-scenery/blob/11090ca6f77413d789cc69b27e70f495d6a44506/Makefile.tiles#L100).

# Download recovery

There's a probablility that downloading a tile may have an issue with the provider (e.g. I had issue with tile +20+110). You can try manually downloading that tile via python3 Ortho4XP.py +/-LAT +/-LON. If O4XP really can't download that file, remove that entry from the tile list - both on the source \<region>_tile_list and \<region>_tile_list.nn. Also remove that tile's folder from O4XP via "rm -rf OSM_data/+20+110 Tiles/zOrtho4XP_+20+110 Elevation_data/+20+110".  Resume by running make -f Makefile.tiles all again. Note that make tries to check the files already downloaded so if you've been running the build for hours, expect some time for make to check the files downloaded before moving forward. If you're running this off a remote drive, you may experience longer resumption. Note, that tiles that were removed may throw an error if your flight needs it enroute.

I will try my best to remove any problematic tiles from the tile lists as humanly possible.
