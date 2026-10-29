# SUPPORT THE DEVELOPMENT OF THIS MOD HERE: https://www.patreon.com/c/BeamNTrack

# BeamNTrack  0.3
BeamNTrack is a BeamNG mod that allows you to track your rides. It gives you statistics of your ride and gives you an oppurnity to see how you drive




## How it works

BeamNTrack has two small parts:

1. **A tiny BeamNG mod** (`beamntrack_mod.zip`) that sends your position, speed, car and weather to your own computer 10 times a second.
2. **A standalone app** (`BeamNTrack.exe` / `app.py`) that listens for that data and draws your route live on the level map.

Everything stays on your PC. Data is sent only to `127.0.0.1` on UDP port `4444`. No account, no server.

## Features

- **Live route map** drawn on the level's own minimap, with roads taken from the level data
- **Zoom, pan and rotate**: wheel zooms at the cursor, drag moves, middle-button drag (or Shift + wheel) rotates, the compass snaps north-up
- **Navigation-arrow marker** that points the way you are driving
- **Live stats**: distance, current and top speed, drive time, moving/stopped time, average speed, climb
- **Weather bar**: in-game clock, dry or raining, wind direction and speed
- **Trip history**: every ride is saved, and you can open, rename or delete it
- **All-time stats**: lifetime totals, plus breakdowns by car and by map
- **Works with any install**: Steam, standalone or another drive (choose the game folder in Settings)
- **Teleport and reset filtering**, so jumping around does not add distance or draw lines across the map
- **Dark and light themes**

## Install

### 1. Install the mod

1. Copy and paste the beamntrack_mod_.zip into your mods folder

### 2. Run the app

**Option A: download the `.exe`** from the [Releases](../../releases) page and run `BeamNTrack.exe`.

**Option B: run from source** (needs Python 3 and Pillow):

```
pip install pillow
python app.py
```

### 3. Drive

Start the app, load any map in BeamNG, spawn a car and drive. The status changes to **Connected** and your route appears.


## Maps

On first use of a level, BeamNTrack reads the minimap pieces and road objects from **your own** BeamNG install, stitches them into one picture and caches it in a `maps` folder next to the app. **No BeamNG assets are included in this repository or in the downloads.**

If BeamNG is not found automatically, open **Settings** and choose the folder that contains `BeamNG.drive.exe`.

## Where your data lives

Next to the app: a `trips` folder (one JSON file per ride), a `maps` folder and `settings.json`. To move the app, move the whole folder.

## Troubleshooting

| Problem | Fix |
| --- | --- |
| Stays on "Waiting for BeamNG..." | Make sure `beamntrack_mod.zip` is in `mods` and **not** unzipped, then restart BeamNG. Open the in-game console (`~`) and look for lines mentioning `beamntrack`. |
| No map, only a grid | The level has no minimap, or the game folder was not found. Set it in Settings. |
| Weather bar is empty | Update to the latest `beamntrack_mod.zip` and restart BeamNG. |
| `python is not recognized` | Reinstall Python and tick **Add python.exe to PATH**. |

## Project layout

```
app.py                 the whole app (Tkinter)
build_exe.py           builds BeamNTrack.exe with PyInstaller
scan_levels.py         helper: lists the levels and minimaps found in your install
mod/                   source of the BeamNG mod
beamntrack_mod.zip     the ready-to-install mod
assets/                logo and icon
```

## Roadmap

- Upload trips to GitHub (private repository or Gist)
- Export a trip as GPX or an image
- Per-trip replay

## Contributing

Issues and pull requests are welcome. If something looks wrong on a particular map, please include the level name and a screenshot.

